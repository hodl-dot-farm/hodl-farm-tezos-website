---
layout: post
title:  "Cycle 203 incident : missed endorsment"
---

On cycle 203 we missed an endorsement.

I lost the logs but observations were:

* public node 1 is stuck with just one connection (to the private node). Public node 0 is healthy and bootstrapped.
* private node looks fine, but it does not process new blocks.
* restarting public node 1 makes it sync, then private node syncs

This is very similar to the [cycle 197 incident](https://github.com/hodl-dot-farm/hodl-farm-tezos-website/blob/master/_posts/2020-02-03-missed_endorsments.md). Back then, the only corrective action we took was to update the tezos container versions, but that obviously was not the problem.

Luckily this time my monitoring worked, so I got alerted and restarted the node straight away.

Taking a further look, connecting to the private node and looking at the connections, it appears that the private node is only connected to one public node:

```
redacted@redacted ~/workspace/tezos () $ ./tezos-client rpc get /network/connections
Disclaimer:
  The  Tezos  network  is  a  new  blockchain technology.
  Users are  solely responsible  for any risks associated
  with usage of the Tezos network.  Users should do their
  own  research to determine  if Tezos is the appropriate
  platform for their needs and should apply judgement and
  care in their network interactions.

[ { "incoming": false, "peer_id": "idredacted",
    "id_point": { "addr": "::ffff:redacted", "port": 9732 },
    "remote_socket_port": 9732,
    "announced_version":
      { "chain_name": "TEZOS_MAINNET", "distributed_db_version": 0,
        "p2p_version": 0 }, "private": false,
    "local_metadata": { "disable_mempool": false, "private_node": true },
    "remote_metadata": { "disable_mempool": false, "private_node": false } } ]

```

Next, opening a shell in the private node and checking the config, I see:

```
~ $ cat /var/run/tezos/node/data/config.json
{ "data-dir": "/var/run/tezos/node/data",
  "rpc": { "listen-addrs": [ ":8732", "0.0.0.0:8732" ] },
  "p2p":
    { "bootstrap-peers":
        [ "boot.tzbeta.net", "tezos-public-node-0.tezos-public-node",
          "tezos-public-node-1.tezos-public-node",
          "tezos-public-node-0.tezos-public-node",
          "tezos-public-node-1.tezos-public-node",
          "tezos-public-node-0.tezos-public-node",
          "tezos-public-node-1.tezos-public-node",
          "tezos-public-node-0.tezos-public-node",
          "tezos-public-node-1.tezos-public-node",
          "tezos-public-node-0.tezos-public-node",
          "tezos-public-node-1.tezos-public-node",
          "tezos-public-node-0.tezos-public-node",
          "tezos-public-node-1.tezos-public-node",
snip
```

This is not good ! It looks like the `--peer` option is cumulative even though I always pass the same argument.

##Takeaways

I observed the same problem where the number of connections goes to zero with the node in the monitoring cluster. The monitoring cluster runs on a twice smaller vm, and it happened more open. So intuitively, this issue is due to the node having insufficient resources.

On the monitoring cluster, we fixed by adding a liveness probe. We know it works, because payouts have been working like clockwork for the past 3-4 cycles.

For the other issue (only one connection), it may be that the peer table has grown so large, and is parsed alphanumerically, so it only tries to connect to one peer.

Looking at the man page, the `--no-bootstrap-peer` option is promising:

```
       --no-bootstrap-peers
           Ignore the peers found in the config file (or the hard-coded
           bootstrap peers in the absence of config file).

```

##corrective action

### For issue 1 : number of connections drops

We are implementing the same liveness check on the public nodes of the baking cluster. However, since in both cases we observed "too few connections (1)", we are considering a node with one connection dead.

Commit: https://github.com/hodl-dot-farm/tezos-on-gke/commit/81b204e1fa59d81def49815751f27cb360d286b6

Checking that it works: it's hard to do since this condition does not happen often.

### For issue 2: private node does not connect to two public nodes

We add the `--no-bootstrap-peer` option. We check config again by opening a shell, we no longer have the endless list. However, doing a rpc call, we still see only one connection !

Looking further at the man page:

```
       --connections=NUM
           Sets min_connections, expected_connections, max_connections to NUM
           / 2, NUM, (3 * NUM) / 2, respectively. Sets peer_table_size to 8 *
           NUM unless it is already defined in the configuration file. Sets
           bootstrap_threshold to min(NUM / 4, 2) unless it is already defined
           in the configuration file.

```

Another commit, we set `--connections 4` so min_connections is 2:

https://github.com/hodl-dot-farm/tezos-on-gke/commit/a97cceb93c477c98499018ed817ba8865e777a98

Sure enough, it works. RPC call reports two connections ! Now, our logs are littered with:

```
│ Feb 26 02:03:01 - p2p.maintenance: Too few connections (2)
│ Feb 26 02:03:06 - p2p.maintenance: Too few connections (2)
│ Feb 26 02:03:11 - p2p.maintenance: Too few connections (2)
│ Feb 26 02:03:16 - p2p.maintenance: Too few connections (2)
│ Feb 26 02:03:21 - p2p.maintenance: Too few connections (2)
│ Feb 26 02:03:26 - p2p.maintenance: Too few connections (2)
│ Feb 26 02:03:31 - p2p.maintenance: Too few connections (2)
│ Feb 26 02:03:36 - p2p.maintenance: Too few connections (2)
│ Feb 26 02:03:41 - p2p.maintenance: Too few connections (2)       
```

To eliminate these warnings, we have to construct the configuration json file ourselves! There is no way to set the right values with cli. However, since it now works, we call it a day.
