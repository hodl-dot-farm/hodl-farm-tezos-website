
---
layout: post
title:  "2019-11-08 Incident: missed endorsment"
---

## Incident report

We missed an endorsment on block [695,414](https://tzstats.com/BMbgvXns9KwdaonHDppBLorQWg2jJS3zsoQZrr9frJeQG2YXUW5) on November 15th.

Regardless of how we perform, we pay you your share as if we were always baking and endorsing 100%. 

That being said, every missed endorsment reflects poorly on us and we want to prevent this from reoccuring.

## Logs

At around 16:25:00 PT, when the block should have been endorsed, the endorser logs say:

```
2019-11-15T22:36:10.808786852Z Nov 15 22:36:10 - 005-PsBabyM1.baking.endorsement: Injected endorsement for block 'BM4jxHJuCvmb' (level 695308, contract k8s-baker) 'ontnggnf58iwzGcy9QpjNcjBu4m2aWS5JEimhkEKgkMTCrPg2Q3'
2019-11-16T00:11:56.525620116Z Nov 16 00:11:56 - 005-PsBabyM1.baking.endorsement: Injected endorsement for block 'BLS65gBrAoAD' (level 695401, contract k8s-baker) 'opNPYk1EVfQ92fUkHNP9gQ9Thai11UCviD586tg7tqrUf2N4f52'
2019-11-16T00:36:44.425740223Z Waiting for the node to synchronize with the network...
```

It appears that the endorser was not aware that block 695,414 was happening. Let's look at the node logs for all containers:

```
E 2019-11-16T00:14:43.224516264Z Nov 16 00:14:43 - main:                   Pushed: 2019-11-16T00:14:41-00:00, Treated: 2019-11-16T00:14:41-00:00, Completed: 2019-11-16T00:14:43-00:00(2019-11-16T00:14:43-00:00)
E 2019-11-16T00:14:43.363668181Z Nov 16 00:14:43 - main: prevalidator-NetXdQprcVkpa-PsBabyM1eUXZ_1 : switching to new head BL6JaE3cHmkntM4JP2VhVs3bymZy7tNjZ7yGbLK3WXnoaYTjShy
E 2019-11-16T00:14:43.363731295Z Nov 16 00:14:43 - main:                                             Pushed: 2019-11-16T00:14:43-00:00, Treated: 2019-11-16T00:14:43-00:00, Completed: 2019-11-16T00:14:43-00:00(2019-11-16T00:14:43-00:00)
E 2019-11-16T00:14:43.366047298Z Nov 16 00:14:43 - main: validator-chain_1 : Update current head to BL6JaE3cHmkntM4JP2VhVs3bymZy7tNjZ7yGbLK3WXnoaYTjShy (fitness 01::0000000000009c6c), same branch
E 2019-11-16T00:14:43.366083301Z Nov 16 00:14:43 - main:                     Pushed: 2019-11-16T00:14:43-00:00, Treated: 2019-11-16T00:14:43-00:00, Completed: 2019-11-16T00:14:43-00:00(2019-11-16T00:14:43-00:00)
E 2019-11-16T00:14:43.422458762Z Nov 16 00:14:43 - 005-PsBabyM1.baking.denunciation: Block BL6JaE3cHmkn registered
E 2019-11-16T00:14:44.019746808Z Nov 16 00:14:44 - 005-PsBabyM1.baking.forge: No slot found at level 695405 (max_priority = 64)
E 2019-11-16T00:20:05.835960978Z Error:
E 2019-11-16T00:20:05.836022012Z   Rpc request failed:
E 2019-11-16T00:20:05.836028680Z      - meth: GET
E 2019-11-16T00:20:05.836034124Z      - uri: http://tezos-remote-signer:8445/keys/tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq
E 2019-11-16T00:20:05.836039500Z      - error: Unexpected error 503:
E 2019-11-16T00:20:05.836044595Z                 "<html><body><h1>503 Service Unavailable</h1>\nNo server is available to handle this request.\n</body></html>\n"
E 2019-11-16T00:20:05.942947083Z    
```

So at 16:14, we load a block, then nothing happens for 10 minutes, then the node restarts. The init container tries to import the key but fails.

That indicates that the signer was unreachable. But one takeaway is that we should not be importing the key if it is already imported. It serves no purpose, and prevent the chain from syncing until this is resolved.

The signer was still not available at 16:25, when the block was supposed to be endorsed:

```
E 2019-11-16T00:25:49.997971019Z Error:
E 2019-11-16T00:25:49.998096787Z   Rpc request failed:
E 2019-11-16T00:25:49.998106260Z      - meth: GET
E 2019-11-16T00:25:49.998111816Z      - uri: http://tezos-remote-signer:8445/keys/tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq
E 2019-11-16T00:25:49.998117256Z      - error: Unexpected error 503:
E 2019-11-16T00:25:49.998122345Z                 "<html><body><h1>503 Service Unavailable</h1>\nNo server is available to handle this request.\n</body></html>\n"
E 2019-11-16T00:25:50.098736264Z    
```

So even if the chain was synced, it would not have helped. Either way, it is something worth solving.

Why did the baking node die at 16:14 ? Let us look at the public node logs:

```
E 2019-11-16T00:14:41.742947796Z Nov 16 00:14:41 - main:                     Pushed: 2019-11-16T00:14:41-00:00, Treated: 2019-11-16T00:14:41-00:00, Completed: 2019-11-16T00:14:41-00:00(2019-11-16T00:14:41-00:00)
I 2019-11-16T00:19:51.488885119Z Current public chain: 2018-06-30T16:07:32Z-betanet.
I 2019-11-16T00:19:51.488949948Z Local chain data: 2018-06-30T16:07:32Z-betanet.
I 2019-11-16T00:19:51.488957645Z Updating the node configuration...
E 2019-11-16T00:19:58.648286010Z Warning: cannot resolve "boot.tzbeta.net"
E 2019-11-16T00:19:58.648325076Z 
``` 

Public node 0 also rebooted at 16:19 and took until 16:23 to sync.

How about public node 1 ?

```
E 2019-11-16T00:24:03.643234455Z Nov 16 00:24:03 - main:                     Pushed: 2019-11-16T00:24:03-00:00, Treated: 2019-11-16T00:24:03-00:00, Completed: 2019-11-16T00:24:03-00:00(2019-11-16T00:24:03-00:00)
E 2019-11-16T00:24:48.582527427Z Nov 16 00:24:48 - main: validator-peer_90 : Worker terminated [validator-peer_90] (2019-11-16T00:24:48-00:00)
E 2019-11-16T00:25:02.009049231Z Nov 16 00:25:02 - main: validator-block : Block BLVAu9r5NsMuT1tS3aQgHPYb4ojn5TDxuW2ZFVhh6eAUdFr8fmK successfully validated
E 2019-11-16T00:25:02.009103731Z Nov 16 00:25:02 - main:                   Pushed: 2019-11-16T00:25:01-00:00, Treated: 2019-11-16T00:25:01-00:00, Completed: 2019-11-16T00:25:02-00:00(2019-11-16T00:25:02-00:00)
E 2019-11-16T00:25:02.084946374Z Nov 16 00:25:02 - main: prevalidator-NetXdQprcVkpa-PsBabyM1eUXZ_1 : switching to new head BLVAu9r5NsMuT1tS3aQgHPYb4ojn5TDxuW2ZFVhh6eAUdFr8fmK
E 2019-11-16T00:25:02.085058269Z Nov 16 00:25:02 - main:                                             Pushed: 2019-11-16T00:25:02-00:00, Treated: 2019-11-16T00:25:02-00:00, Completed: 2019-11-16T00:25:02-00:00(2019-11-16T00:25:02-00:00)
E 2019-11-16T00:25:02.086512792Z Nov 16 00:25:02 - main: validator-chain_1 : Update current head to BLVAu9r5NsMuT1tS3aQgHPYb4ojn5TDxuW2ZFVhh6eAUdFr8fmK (fitness 01::0000000000009c75), same branch
```

Public node 1 was alive and well.

It starts to look like all containers of one vm died, while the other vm was mostly fine.

Indeed, we find a node that runs public-node-0 and private-node.

Looking at these node logs, we find a drop of CPU and Memory right at the time this reboot happened.

There are no kubernetes events around the period. It is hard to tell what happened.

## Signer forwarder

However, one thing is for sure, if the signer was reachable when the node started, we would have endorsed this block ! Let's take a closer look at the signer-forwarder.

```
E 2019-11-15T19:18:24.100029880Z [WARNING] 318/191824 (7) : Server tezos-signer-backends/tezos-signer-1 is UP, reason: Layer7 check passed, code: 200, info: "HTTP status check returned code <3C>200<3E>", check duration: 90ms. 1 active and 0 backup servers online. 0 sessions requeued, 0 total in queue.
E 2019-11-15T19:18:31.423174558Z [WARNING] 318/191831 (7) : Server tezos-signer-backends/tezos-signer-2 is UP, reason: Layer7 check passed, code: 200, info: "HTTP status check returned code <3C>200<3E>", check duration: 92ms. 2 active and 0 backup servers online. 0 sessions requeued, 0 total in queue.
E 2019-11-16T00:16:38.079106642Z Accepted publickey for signer from 10.0.96.30 port 36418 ssh2: RSA SHA256:Kmxyimy0FGCK8XV05olM7xqSUK3Hm1UcK1ZbYy7ogto
E 2019-11-16T00:16:38.125608248Z bind [::]:65050: Address in use
E 2019-11-16T00:16:38.125672864Z channel_setup_fwd_listener_tcpip: cannot listen to port: 65050
E 2019-11-16T00:16:38.125744834Z bind [::]:8443: Address in use
E 2019-11-16T00:16:38.125756588Z channel_setup_fwd_listener_tcpip: cannot listen to port: 8443
E 2019-11-16T00:16:38.125763232Z bind [::]:9443: Address in use
E 2019-11-16T00:16:38.125769525Z channel_setup_fwd_listener_tcpip: cannot listen to port: 9443
E 2019-11-16T00:16:38.479223979Z Accepted publickey for signer from 10.0.96.30 port 52414 ssh2: RSA SHA256:MgQASa9iMmjsdTRcVJo/GPX18AErnp78gNj1g7s3l90
```
 
Nothing had happened since morning, and suddenly, the signers attempt to reconnect. But then the port is already bound because the ssh server thinks that the previous connection is still active !

This is confirmed by observing the logs on the Raspberry Pi:

```
Nov 15 19:17:50 raspberrypi autossh[19515]: ssh exited with error status 255; restarting ssh
Nov 15 19:18:05 raspberrypi autossh[19515]: starting ssh (count 1793)
Nov 15 19:18:05 raspberrypi autossh[19515]: ssh child pid is 5137
Nov 16 00:16:22 raspberrypi autossh[19515]: timeout polling to accept read connection
Nov 16 00:16:22 raspberrypi autossh[19515]: port down, restarting ssh
Nov 16 00:16:22 raspberrypi autossh[19515]: starting ssh (count 1794)
Nov 16 00:16:22 raspberrypi autossh[19515]: ssh child pid is 23287
Nov 16 00:16:37 raspberrypi autossh[19515]: 127.0.0.1:65050: Connection refused
Nov 16 00:16:37 raspberrypi autossh[19515]: port down, restarting ssh
Nov 16 00:16:37 raspberrypi autossh[19515]: starting ssh (count 1795)
Nov 16 00:16:37 raspberrypi autossh[19515]: ssh child pid is 23303
Nov 16 00:16:38 raspberrypi autossh[19515]: Warning: remote port forwarding failed for listen port 65050
Nov 16 00:16:38 raspberrypi autossh[19515]: Warning: remote port forwarding failed for listen port 8443
Nov 16 00:16:38 raspberrypi autossh[19515]: Warning: remote port forwarding failed for listen port 9443
Nov 16 00:16:59 raspberrypi autossh[19515]: timeout polling to accept read connection
Nov 16 00:16:59 raspberrypi autossh[19515]: port down, restarting ssh
Nov 16 00:16:59 raspberrypi autossh[19515]: starting ssh (count 1796)
Nov 16 00:16:59 raspberrypi autossh[19515]: ssh child pid is 23325
Nov 16 00:17:00 raspberrypi autossh[19515]: Warning: remote port forwarding failed for listen port 65050
Nov 16 00:17:00 raspberrypi autossh[19515]: Warning: remote port forwarding failed for listen port 8443
Nov 16 00:17:00 raspberrypi autossh[19515]: Warning: remote port forwarding failed for listen port 9443
```

And so on so forth, the client keeps trying to connect and it always fails with the same error message: remote port forwarding failed.

We have a clear problem, that the client watches the ssh connection for timeouts, but the server does not do that, so it keeps the forwarded port bound until the tcp session times out !

The solution seems to be to configure ssh daemon (on the Cloud side) with ClientAliveInterval = 10 so it kills the ssh daemon (and frees the ports !) after 3 unresponsive pings, or 30 seconds.

After applying the change:

https://github.com/hodl-dot-farm/tezos-on-gke/commit/8a84ab442a0b9e34847b8d7b3d4bd949456d6147

We perform a test, unplugging the raspberry pi from the switch, letting the LTE connection take over, then, sure enough, the server-side times out and reconnection happens after less than a minute:

```
tezos-remote-signer-forwarder Accepted publickey for signer from 10.0.96.30 port 46488 ssh2: RSA SHA256:Kmxyimy0FGCK8XV05olM7xqSUK3Hm1UcK1ZbYy7ogto
tezos-remote-signer-loadbalancer [WARNING] 319/074920 (7) : Server tezos-signer-backends/tezos-signer-1 is UP, reason: Layer7 check passed, code: 200, info: "HTTP status check returned code <3C>200<3E>", check duration: 93ms. 2 active and 0 backup servers online. 0 sessions requeued, 0 total in queue.
tezos-remote-signer-forwarder Accepted publickey for signer from 10.0.96.31 port 26648 ssh2: RSA SHA256:Kmxyimy0FGCK8XV05olM7xqSUK3Hm1UcK1ZbYy7ogto
tezos-remote-signer-forwarder bind [::]:50799: Address in use
tezos-remote-signer-forwarder channel_setup_fwd_listener_tcpip: cannot listen to port: 50799
tezos-remote-signer-forwarder bind [::]:8443: Address in use
tezos-remote-signer-forwarder channel_setup_fwd_listener_tcpip: cannot listen to port: 8443
tezos-remote-signer-forwarder bind [::]:9443: Address in use
tezos-remote-signer-forwarder channel_setup_fwd_listener_tcpip: cannot listen to port: 9443
tezos-remote-signer-forwarder Timeout, client not responding from user signer 10.0.96.30 port 46488
tezos-remote-signer-forwarder Received disconnect from 10.0.96.31 port 26648:11: disconnected by user
tezos-remote-signer-forwarder Disconnected from user signer 10.0.96.31 port 26648
tezos-remote-signer-forwarder Accepted publickey for signer from 10.0.96.30 port 53654 ssh2: RSA SHA256:Kmxyimy0FGCK8XV05olM7xqSUK3Hm1UcK1ZbYy7ogto
tezos-remote-signer-forwarder Accepted publickey for signer from 10.0.96.30 port 46530 ssh2: RSA SHA256:Kmxyimy0FGCK8XV05olM7xqSUK3Hm1UcK1ZbYy7ogto
tezos-remote-signer-forwarder bind [::]:50799: Address in use
tezos-remote-signer-forwarder channel_setup_fwd_listener_tcpip: cannot listen to port: 50799
tezos-remote-signer-forwarder bind [::]:8443: Address in use
tezos-remote-signer-forwarder channel_setup_fwd_listener_tcpip: cannot listen to port: 8443
tezos-remote-signer-forwarder bind [::]:9443: Address in use
tezos-remote-signer-forwarder channel_setup_fwd_listener_tcpip: cannot listen to port: 9443
tezos-remote-signer-forwarder Timeout, client not responding from user signer 10.0.96.30 port 53654
tezos-remote-signer-forwarder connect_to 127.0.0.1 port 50799: failed.
tezos-remote-signer-forwarder Received disconnect from 10.0.96.30 port 46530:11: disconnected by user
tezos-remote-signer-forwarder Disconnected from user signer 10.0.96.30 port 46530
tezos-remote-signer-forwarder Accepted publickey for signer from 10.0.96.31 port 46534 ssh2: RSA SHA256:Kmxyimy0FGCK8XV05olM7xqSUK3Hm1UcK1ZbYy7ogto 
```

## Baker node init container

Pushed a fix to not require the signer to be online at every boot. It's only required for the first ever boot of the baker, but any subsequent boots will disregard absence of the signer until it is actually time to sign.
