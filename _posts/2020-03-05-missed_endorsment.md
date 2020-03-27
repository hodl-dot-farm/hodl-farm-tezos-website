---
layout: post
title: "Incident: missed endorsement"
date: "2019-03-05"
category: Incidents
---

Missed endorsement 
 
From the endorser 
 
``` 
│ Bootstrapped.                                                                                                                                                                                                    │ 
│ Waiting for the node to be synchronized with its peers...                                                                                                                                                        │ 
│ Node synchronized.                                                                                                                                                                                               │ 
│ Endorser started.                                                                                                                                                                                                │ 
│ Mar  5 05:24:24 - 006-PsCARTHA.baking.endorsement: Error while injecting endorsement for delegate tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq :                                                                         │ 
│ Mar  5 05:24:24 - 006-PsCARTHA.baking.endorsement:   Error:                                                                                                                                                      │ 
│ Mar  5 05:24:24 - 006-PsCARTHA.baking.endorsement:     Rpc request failed:                                                                                                                                       │ 
│ Mar  5 05:24:24 - 006-PsCARTHA.baking.endorsement:        - meth: GET                                                                                                                                            │ 
│ Mar  5 05:24:24 - 006-PsCARTHA.baking.endorsement:        - uri: http://tezos-remote-signer:8445/authorized_keys                                                                                                 │ 
│ Mar  5 05:24:24 - 006-PsCARTHA.baking.endorsement:        - error: Unexpected error 503:                                                                                                                         │ 
│ Mar  5 05:24:24 - 006-PsCARTHA.baking.endorsement:                   "<html><body><h1>503 Service Unavailable</h1>\nNo server is available to handle this request.\n</body></html>\n"                            │ 
│ Mar  5 05:24:24 - 006-PsCARTHA.baking.endorsement:                                                                                                                                                               │ 
│ Mar  5 05:24:24 - 006-PsCARTHA.baking.endorsement:       
``` 
 
From the load balancer : no servers available 
 
Attempt to ssh to the signer.  
 
``` 
me@peck ~/workspace/tezos-on-gke/terraform () $ ssh -p 9443 tezos@localhost 
kex_exchange_identification: Connection closed by remote host 
``` 
 
The tunnels are not established. 
 
Forwarder logs: 
 
``` 
│ Connection closed by 10.0.96.10 port 49478 [preauth]                                                                                                                                                             │ 
│ Connection closed by 10.0.96.2 port 39926 [preauth]                                                                                                                                                              │ 
│ Connection closed by 10.0.96.10 port 49480 [preauth]                                                                                                                                                             │ 
│ Connection closed by 10.0.96.2 port 39928 [preauth]  
``` 
 
Earlier today, I pushed new containers for everything. This one must have changed ? 
 
I tag an older container with the "knownworking" tag and everything works again. 
 
Then 
 
``` 
│ Mar  5 19:15:54 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:15:59 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:16:04 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:16:09 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:16:14 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:16:19 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:16:24 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:16:29 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:16:34 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:16:39 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:16:44 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:16:49 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:16:54 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:16:59 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:17:04 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:17:09 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:17:14 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:17:19 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:17:24 - p2p.maintenance: Too few connections (0)                                                                                                                                                       │ 
│ Mar  5 19:17:29 - p2p.maintenance: Too few connections (0)    
``` 
 
Restarting the private node fixes it. 
 
A little bit later, I observe that only one connection is established: 
 
``` 
│ Mar  5 23:01:18 - p2p.maintenance: Too few connections (1)                                                                                                                                                       │ 
│ Mar  5 23:01:23 - p2p.maintenance: Too few connections (1)                                                                                                                                                       │ 
│ Mar  5 23:01:28 - p2p.maintenance: Too few connections (1)                                                                                                                                                       │ 
│ Mar  5 23:01:33 - p2p.maintenance: Too few connections (1)                                                                                                                                                       │ 
│ Mar  5 23:01:38 - p2p.maintenance: Too few connections (1)           
``` 
 
Debugging with the admin command I see one connection is established and another one is not: 
 
``` 
me@peck ~/workspace/tezos () $ ./tezos-admin-client  p2p stat
GLOBAL STATS
  ↗ 164.33 kiB (945 B/s) ↘ 346.49 kiB (1.21 kiB/s)
CONNECTIONS
  ↗ idrXWNkRhaJkNiDn3v32R8sT7CLcVJ 10.0.93.2:9732 (TEZOS_MAINNET.0 (p2p: 0))
KNOWN PEERS
  ⚌  1 idrXWNkRhaJkNiDn3v32R8sT7CLcVJ ↗ 161.43 kiB (672 B/s) ↘ 343.65 kiB (877 B/s)
  ⚏  1 idqfVdug3GZvqFkPAVZMd4WxjbP425 ↗ 0 B (0 B/s) ↘ 0 B (0 B/s)
KNOWN POINTS
  ⚏  10.0.92.17:9732
  ⚌  10.0.93.2:9732 idrXWNkRhaJkNiDn3v32R8sT7CLcVJ ★
```

Port-forwarding the public node, I check that the private node is not banned. It is not:

```
me@peck ~/workspace/tezos () $ ./tezos-admin-client  is peer banned idr8ew6eoD2ZUHm8xvYTBThBbkZS6T
The given peer ID is not banned
```

It just does not connect to the private node. I am wondering whether bi-directional communication should be established between public and private just to force connection.

Trying by-directional connection
--------------------------------

We remove --private-mode from the command line. Reasoning is: the node is effectively private if it only knows about two nodes passed as argument and does not query the bootstrap peer. This argument is not necessary because the infrastructure achieves the same goal.

The protocol is: we restart the private node. It connects to the public nodes (OK, that works already). We then restart the public node. The expectation is that the public node should reconnect immediately to the private node. However, this is not happening:

```
│ Mar  5 23:48:47 - p2p.maintenance: Too few connections (2)                                                                                                                                                       │
│ Mar  5 23:48:52 - p2p.maintenance: Too few connections (2)                                                                                                                                                       │
│ Mar  5 23:48:55 - validator.peer: Worker terminated [validator-peer_2]                                                                                                                                           │
│ Mar  5 23:48:57 - p2p.maintenance: Too few connections (1)                                                                                                                                                       │
│ Mar  5 23:49:02 - p2p.maintenance: Too few connections (1)                                                                                                                                                       │
│ Mar  5 23:49:07 - p2p.maintenance: Too few connections (1)                                                                                                                                                       │
│ Mar  5 23:49:12 - p2p.maintenance: Too few connections (1)                                                                                                                                                       │
│ Mar  5 23:49:17 - p2p.maintenance: Too few connections (1)                                                                                                                                                       │
│ Mar  5 23:49:21 - p2p.connection-pool: [private node] incoming connection from untrused peer rejected!                                                                                                           │
│ Mar  5 23:49:21 - p2p.maintenance: Too few connections (1)                                                                                                                                                       │
│ Mar  5 23:49:26 - p2p.maintenance: Too few connections (1)                                                                                                                                                       │
│ Mar  5 23:49:31 - p2p.maintenance: Too few connectionso
```

It still thinks it's in private mode. That is because our configuration is sticky. We take the opportunity to implement the TODO from last outage, and actually hard-code the tezos-node configuration file in the init container. This way, no more stickiness, and we stop passing all these args.

We retry and aaargh ! The storage is corrupted. Too many restarts. We hit this bug: https://gitlab.com/tezos/tezos/issues/704

No biggie, we sync the chain from a snapshot and try again.

We still see: 
```
│ Mar  6 06:09:52 - p2p.maintenance: Too few connections (2)                                                                                                                                                       │
│ Mar  6 06:10:12 - p2p.maintenance: Too few connections (2)
```

so we have to tweak params some more ! Setting min-connections to 1 and desired connections to 2, we get the expected behavior : no logs when connections are 2, but logs are spewed when there are 0 or 1 connection.
