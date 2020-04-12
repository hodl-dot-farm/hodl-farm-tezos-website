#Missed endorsment

Endorser logs shows no event between 23:00, Apr 9 and 05:00, Apr 10 CST:

```

Error
2020-04-09 23:00:42.454 CST
Apr 9 15:00:42 - 006-PsCARTHA.baking.endorsement: Injected endorsement for block 'BKpHpVmdkbnR' (level 901977, contract k8s-baker) 'oodo9FF7vQKxDzRmyZ4X47kypfBfnQEB5msSFehDMcJ4Grb8xWC'
Info
2020-04-10 05:11:50.688 CST
Waiting for the node to initialize...... done.
```

Private node logs reveal that it was working with just one connection for a while, when for 5 minutes it went down to zero connections:

```
Error
2020-04-10 00:24:18.812 CST
Apr 9 16:24:18 - p2p.maintenance: Too few connections (1)
Error
2020-04-10 00:24:28.818 CST
Apr 9 16:24:28 - p2p.maintenance: Too few connections (1)
Error
2020-04-10 00:24:34.293 CST
Apr 9 16:24:34 - validator.peer: Worker terminated [validator-peer_4]
Error
2020-04-10 00:24:43.820 CST
Apr 9 16:24:43 - p2p.maintenance: Too few connections (0)
Error
2020-04-10 00:24:58.822 CST
Apr 9 16:24:58 - p2p.maintenance: Too few connections (0)
Error
2020-04-10 00:25:13.824 CST
Apr 9 16:25:13 - p2p.maintenance: Too few connections (0)
Error
2020-04-10 00:25:28.827 CST
Apr 9 16:25:28 - p2p.maintenance: Too few connections (0)
Error
2020-04-10 00:25:43.829 CST
Apr 9 16:25:43 - p2p.maintenance: Too few connections (0)
Error
2020-04-10 00:25:58.830 CST
Apr 9 16:25:58 - p2p.maintenance: Too few connections (0)
Error
2020-04-10 00:26:13.832 CST
Apr 9 16:26:13 - p2p.maintenance: Too few connections (0)
Error
2020-04-10 00:26:28.834 CST
Apr 9 16:26:28 - p2p.maintenance: Too few connections (0)
Error
2020-04-10 00:26:43.836 CST
Apr 9 16:26:43 - p2p.maintenance: Too few connections (0)
Error
2020-04-10 00:26:58.840 CST
Apr 9 16:26:58 - p2p.maintenance: Too few connections (0)
Error
2020-04-10 00:27:13.843 CST
Apr 9 16:27:13 - p2p.maintenance: Too few connections (0)
Error
2020-04-10 00:27:28.845 CST
Apr 9 16:27:28 - p2p.maintenance: Too few connections (0)
Error
2020-04-10 00:27:43.848 CST
Apr 9 16:27:43 - p2p.maintenance: Too few connections (0)
Error
2020-04-10 00:27:58.850 CST
Apr 9 16:27:58 - p2p.maintenance: Too few connections (0)
Error
2020-04-10 00:28:05.480 CST
Apr 9 16:28:05 - validator.peer: Worker started for NetXdQprcVkpa:idsnip
```

Then the connection got reestablished.

That would not have happened if the node always had 2 connections.

Corrective action is to consider the "one connection state" as abnormal and rekick the node when it happens. Effectively, that means restarting the private node each time a public node restarts and two connections can not be established.
