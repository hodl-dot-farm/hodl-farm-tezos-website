---
layout: post
title:  "Cycle 197 Incident: Missed endorsments"
---

Private node logs:

```
Warning: cannot resolve "tezos-public-node-1.tezos-public-node"                                                                                                                                                                                            │
│                                                                                                                                                                                                                                                            │
│ Warning: cannot resolve "tezos-public-node-1.tezos-public-node"                                                                                                                                                                                            │
│                                                                                                                                                                                                                                                            │
│ Warning: cannot resolve "tezos-public-node-0.tezos-public-node"                                                                                                                                                                                            │
│                                                                                                                                                                                                                                                            │
│ Warning: cannot resolve "tezos-public-node-1.tezos-public-node"                                                                                                                                                                                            │
│                                                                                                                                                                                                                                                            │
│ Warning: cannot resolve "tezos-public-node-1.tezos-public-node"                                                                                                                                                                                            │
│                                                                                                                                                                                                                                                            │
│ Warning: cannot resolve "tezos-public-node-1.tezos-public-node"                                                                                                                                                                                            │
│                                                                                                                                                                                                                                                            │
│ Warning: cannot resolve "tezos-public-node-1.tezos-public-node"                                                                                                                                                                                            │
│                                                                                                                                                                                                                                                            │
│ Warning: cannot resolve "tezos-public-node-0.tezos-public-node"                                                                                                                                                                                            │
│                                                                                                                                                                                                                                                            │
│ Warning: cannot resolve "tezos-public-node-0.tezos-public-node"                                                                                                                                                                                            │
│                                                                                                                                                                                                                                                            │
│ Warning: cannot resolve "tezos-public-node-0.tezos-public-node"                                                                                                                                                                                            │
│                                                                                                                                                                                                                                                            │
│ Warning: cannot resolve "tezos-public-node-1.tezos-public-node"                                                                                                                                                                                            │
│                                                                                                                                                                                                                                                            │
│ Warning: cannot resolve "tezos-public-node-0.tezos-public-node"                                                                                                                                                                                            │
│                                                                                                                                                                                                                                                            │
│ Warning: cannot resolve "tezos-public-node-0.tezos-public-node"                                                                                                                                                                                            │
│                                                                                                                                                                                                                                                            │
│ Feb  2 22:59:39 - node.main: Starting the Tezos node...                                                                                                                                                                                                    │
│ Feb  2 22:59:39 - node.main: No local peer discovery.                                                                                                                                                                                                      │
│ Feb  2 22:59:41 - node.main: Peer's global id: idr8ew6eoD2ZUHm8xvYTBThBbkZS6T                                                                                                                                                                              │
│ Feb  2 22:59:41 - main: shell-node initialization: bootstrapping                                                                                                                                                                                           │
│ Feb  2 22:59:42 - main: shell-node initialization: p2p_maintain_started                                                                                                                                                                                    │
│ Feb  2 22:59:42 - shell.validation_process.external: Initialized                                                                                                                                                                                           │
│ Feb  2 22:59:42 - shell.validation_process.external: Block validation started on pid 176                                                                                                                                                                   │
│ Feb  2 22:59:57 - validator.block: Worker started                                                                                                                                                                                                          │
│ Feb  2 22:59:57 - node.validator: activate chain NetXdQprcVkpaWU                                                                                                                                                                                           │
│ Feb  2 22:59:57 - p2p.maintenance: Too few connections (0)                                                                                                                                                                                                 │
│ Feb  2 22:59:57 - validator.chain: Worker started for NetXdQprcVkpa                                                                                                                                                                                        │
│ Feb  2 22:59:57 - prevalidator.NetXdQprcVkpa.PsBabyM1eUXZ: Worker started for NetXdQprcVkpa.PsBabyM1eUXZ                                                                                                                                                   │
│ Feb  2 22:59:58 - node.main: Starting a RPC server listening on ::ffff:0.0.0.0:8732.                                                                                                                                                                       │
│ Feb  2 23:00:02 - node.main: Starting a RPC server listening on :::8732.                                                                                                                                                                                   │
│ Feb  2 23:00:02 - node.main: The Tezos node is now running!                                                                                                                                                                                                │
│ Feb  2 23:00:02 - validator.peer: Worker started for NetXdQprcVkpa:idqfVdug3GZv                                                                                                                                                                            │
│                                                                                               
```

Public node 0 logs:

```
E 2020-02-03T09:20:44.990851263Z Feb  3 09:20:44 - p2p.maintenance: Too few connections (1)
 
 E 2020-02-03T09:20:49.991902198Z Feb  3 09:20:49 - p2p.maintenance: Too few connections (1)
  
  E 2020-02-03T09:20:54.992661853Z Feb  3 09:20:54 - p2p.maintenance: Too few connections (1)
   
   E 2020-02-03T09:20:59.993725385Z Feb  3 09:20:59 - p2p.maintenance: Too few connections (1)
    
    E 2020-02-03T09:21:04.994184444Z Feb  3 09:21:04 - p2p.maintenance: Too few connections (1)
     
     E 2020-02-03T09:21:09.994360124Z Feb  3 09:21:09 - p2p.maintenance: Too few connections (1)
      
      E 2020-02-03T09:21:14.994889423Z Feb  3 09:21:14 - p2p.maintenance: Too few connections (1)
       
       E 2020-02-03T09:21:19.996065259Z Feb  3 09:21:19 - p2p.maintenance: Too few connections (1)
        
        E 2020-02-03T09:21:24.996474841Z Feb  3 09:21:24 - p2p.maintenance: Too few connections (1)
         
         E 2020-02-03T09:21:29.997280447Z Feb  3 09:21:29 - p2p.maintenance: Too few connections (1)
          
          E 2020-02-03T09:21:34.997846198Z Feb  3 09:21:34 - p2p.maintenance: Too few connections (1)
           
           E 2020-02-03T09:21:39.998196317Z Feb  3 09:21:39 - p2p.maintenance: Too few connections (1)
            
            E 2020-02-03T09:21:44.999068668Z Feb  3 09:21:44 - p2p.maintenance: Too few connections (1)
             
             E 2020-02-03T09:21:49.999989822Z Feb  3 09:21:49 - p2p.maintenance: Too few connections (1)
              
              E 2020-02-03T09:21:55.001070084Z Feb  3 09:21:55 - p2p.maintenance: Too few connections (1)
               
               E 2020-02-03T09:22:00.001390287Z Feb  3 09:22:00 - p2p.maintenance: Too few connections (1)
                
                E 2020-02-03T09:22:05.002205535Z Feb  3 09:22:05 - p2p.maintenance: Too few connections (1)
                 
```

Public node 1 logs:

```
E 2020-02-03T09:25:01.131712780Z Feb  3 09:25:01 - validator.chain: Request pushed on 2020-02-03T09:25:01-00:00, treated in 6.455ms, completed in 53.447ms 
 
 E 2020-02-03T09:25:46.498513603Z Feb  3 09:25:46 - updater: COMPILATION ERROR (/var/run/tezos/node/data/protocol/PsCARTHAGazK/LOG)
  
  E 2020-02-03T09:26:00.996866191Z Feb  3 09:26:00 - validator.block: Block BMM3sAqoyrXz487GaNjyCFaNQhhpfE3wRiXGVxd9r9heSrbGgCA successfully validated
   
   E 2020-02-03T09:26:00.996900860Z Feb  3 09:26:00 - validator.block: Request pushed on 2020-02-03T09:26:00-00:00, treated in 82.923us, completed in 391ms 
    
    E 2020-02-03T09:26:01.046783639Z Feb  3 09:26:01 - prevalidator.NetXdQprcVkpa.PsBabyM1eUXZ: switching to new head BMM3sAqoyrXz487GaNjyCFaNQhhpfE3wRiXGVxd9r9heSrbGgCA
     
     E 2020-02-03T09:26:01.046821640Z Feb  3 09:26:01 - prevalidator.NetXdQprcVkpa.PsBabyM1eUXZ:  Request pushed on 2020-02-03T09:26:01-00:00, treated in 4.391ms, completed in 21.668ms 
      
      E 2020-02-03T09:26:01.047254517Z Feb  3 09:26:01 - validator.chain: Update current head to BMM3sAqoyrXz487GaNjyCFaNQhhpfE3wRiXGVxd9r9heSrbGgCA (fitness 01::000000000002550a), same branch
       
       E 2020-02-03T09:26:01.047277517Z Feb  3 09:26:01 - validator.chain: Request pushed on 2020-02-03T09:26:00-00:00, treated in 6.29ms, completed in 44.136ms 
        
        E 2020-02-03T09:26:45.681548061Z Feb  3 09:26:45 - updater: COMPILATION ERROR (/var/run/tezos/node/data/protocol/PsCARTHAGazK/LOG)
         
         E 2020-02-03T09:27:17.576016874Z Feb  3 09:27:17 - validator.block: Block BLchgxfcsiTLMSrTTZUabQm751anx3uopxCFhsuRMJu2nSxwv7Q successfully validated
          

```

Observation: public node 1 is complaining about CARTHAGE which is the next protocol version. Perhaps it's time to update the node.

Observation: public node 1 is up-to-date, but the block do not seem to propagate to the private node. Private node is stuck at zero connections.

Observation: other bakers seem to have had the same issue https://tzstats.com/tz1LBEKXaxQbd5Gtzbc1ATCwc3pppu81aWGc (cycle 197)

Restarting the setup:

Restarted public node 0 : it syncs, and the private node syncs too.

Observed that public node after syncing gives the same COMPILATION ERROR with carthage protocol. Not sure if it's related to the issue at hand.

Corrective action:

Upgraded the tezos conainers for the sentry nodes to the most recent version to attempt to eliminate the carthage issue. The error message indeed disappears.
