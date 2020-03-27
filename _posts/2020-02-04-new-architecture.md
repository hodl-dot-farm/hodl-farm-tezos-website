---
layout: post
title: "Architecture update"
date: "2020-02-04"
category: "Architectural Changes"
---

All hail the new Kubernetes baking infrastructure ! It comes baked in with operational knowledge learned by several months of operating a medium-sized baking operation.

Our first iteration of the Tezos-on-GKE project had the payout pod connecting to the baking node to send the payouts.

We later added an additional full node in a separate cluster to monitor the entire baking operation using the excellent Tezos-network-monitor from Polychain Labs to get Slack and Pagerduty alerts when something goes wrong with the baking operations.

The main reason to set up the monitoring node in a separate cluster is isolation. If your main cluster goes down, you do not want your monitoring cluster to go down as well and go blind ! It also protects from operational (human) error. The baking operations are of paramount importance, so if you work on the payout cluster, it remains operational no matter what.

We realized that payouts, while important, are also less critical than the baking operations. So we moved the payouts outside of the baker cluster and into the monitoring cluster, which we renamed the "auxiliary" cluster.

Now the baking cluster only concerns itself with baking and endorsing, all auxiliary operations like payout and website generation belong to the auxiliary cluster. The code is also separated more cleanly: if all you want is to set up a baker, you only need to deploy the tezos-on-gke codebase.

Storage backends
----------------

Our baking infrastructure now uses three different storage backends:

* snapshot for the sentry nodes (public-facing nodes)
* full for the baking node
* archive for the payout node

We found that the memory and CPU requirements of running a full node are lower than running in archive mode. Besides, archive node is only needed to query the delegation information in the past for payout purposes. It is not needed for baking. Consequently, baking in "full" mode is the right thing to do. It also reduces the disk space requirements of the baking cluster.

Payout as cronjob
-----------------

We were previously running backerei in "continuous" mode. But why have a continuously running process in the cluster while it is only needed for a few minutes every three days ? We replace the payout StatefulSet with a kubernetes CronJob.

It makes the setup more reliable, because the continous mode assumes that the node is online and up-to-date when the new cycle begins. We observed that nodes tend to die due to resource constraints right at the beginning of a new cycle, because every baker does payouts and transactions abound !

We had cases of the node being offline exactly on a new cycle. When that happens, and the node restarts and catches up, a payout is sent right when backerei thinks a new cycle is starting, even though the node is still bootstrapping. That results in dummy operations that are never included in a block, but backerei thinks that the payouts are done and puts the dummy operation hash in the database! No longer: our wrapper script now checks that the node is bootstrapped before doing any payout operation !

Double payouts ahoy !
---------------------

We had two double payout incidents, one on Dec 24th (Christmas present) and January 25th (Chinese new year present).

In one case, we observed that the node became offline in the middle of the payout operation, resulting in a crash. The payout operation itself was successful, but the database was not updated with the operation hash.

In another case, we had the payouts go out twice, once when they got estimated and another time when they got finalized ! We are still working on a series of backerei patches to make it work seamlessly with our unusual payout model (pay rewards before they are finalized). As of now, we have to manualy update the payout starting cycle variable every few cycles to avoid such scenarios ! In that specific case, we did not do that on time.

As much as we love to give free money to our delegates, we have now implemented a check: for a given witness address, if any payout has already been done during the cycle, we abort the payout operation.

The node has no API to query an answer to the question "Has a payout from X to Y been done during this cycle ?". An indexer is needed. Instead of hosting our own tezos indexer, we are using the superb api from tzstats. This way, no more double payouts ever ! Sorry !

Probing for connection numbers
------------------------------

The next recurring issue in the setup is that the connection count on the nodes often drops to zero or one. That means that the Tezos nodes are no longer part of the peer-to-peer network, even though from Kubernetes point of view they work fine.

This happens more often in the monitoring cluster, resulting in delayed payouts. It is interesing, since the monitoring cluster is less powerful than the baking cluster - it is using lower tier GCP virtual machines. It must be that the nodes are starved of resources. One easy way out would be to throw money at the problem, use beefier virtual machines and we would expect less cases where the nodes just loose all their connections. But we are all about baking on the cheap here, so an acceptable workaround is to rekick the nodes when they get into this state.

To achieve that, we have introduced a liveness probe. It runs a shell script inside the Tezos node container, which queries the number of connections. When they drop below a threshold and stay at this threshold for a set amount of time, we kill the pod. Kubernetes then restarts it.

This liveness probe has been deployed on cycle 198 on the monitoring/payout cluster, and since then, we have not had to restart the node manually, and payouts have been transferred on time, like clockwork, without any manual intervention. Hooray!
