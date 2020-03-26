Missed endorsment in cycle 203

One of the public nodes (public-node-1) is stuck with one connection only

```
│ tezos-public-node Feb 21 20:19:50 - p2p.maintenance: Too few connections (1)                                                                                                                                                                               │
│ tezos-public-node Feb 21 20:19:55 - p2p.maintenance: Too few connections (1)                                                                                                                                                                               │
│ tezos-public-node Feb 21 20:20:00 - p2p.maintenance: Too few connections (1)                                                                                                                                                                               │
│ tezos-public-node Feb 21 20:20:05 - p2p.maintenance: Too few connections (1)                                                                                                                                                                               │
│ tezos-public-node Feb 21 20:20:10 - p2p.maintenance: Too few connections (1)                                                                                                                                                                               │
│ tezos-public-node Feb 21 20:20:15 - p2p.maintenance: Too few connections (1)                                                                                                                                                                               │
│ tezos-public-node Feb 21 20:20:20 - p2p.maintenance: Too few connections (1)                                                                                                                                                                               │
│ tezos-public-node Feb 21 20:20:25 - p2p.maintenance: Too few connections (1)                                                                                                                                                                               │
│ tezos-public-node Feb 21 20:20:30 - p2p.maintenance: Too few connections (1)                                                                                                                                                                               │
│ tezos-public-node Feb 21 20:20:35 - p2p.maintenance: Too few connections (1)                                                                                                                                                                               │
│ tezos-public-node Feb 21 20:20:40 - p2p.maintenance: Too few connections (1)                                                                                                                                                                               │
│ tezos-public-node Feb 21 20:20:45 - p2p.maintenance: Too few connections (1)                                                                                                                                                                               │
│ tezos-public-node Feb 21 20:20:50 - p2p.maintenance: Too few connections (1)                                                                                                                                                                               │
│ tezos-public-node Feb 21 20:20:55 - p2p.maintenance: Too few connections (1)                                                                                                                                                                               │
│                                                                                   
```

Looking further back I see:

```
E 2020-02-21T17:04:43.442316464Z Feb 21 17:04:43 - p2p.maintenance: Too few connections (1)
 
 E 2020-02-21T17:04:45.159743177Z Feb 21 17:04:45 - validator.block: Block BL1idjBg72ybk9DaD18StENbdT4cZb72E41VB9yiNUfN1LSud8n successfully validated
  
  E 2020-02-21T17:04:45.159776610Z Feb 21 17:04:45 - validator.block: Request pushed on 2020-02-21T17:04:44-00:00, treated in 10.799us, completed in 843ms 
   
   E 2020-02-21T17:04:45.212314496Z Feb 21 17:04:45 - prevalidator.NetXdQprcVkpa.PsBabyM1eUXZ: switching to new head BL1idjBg72ybk9DaD18StENbdT4cZb72E41VB9yiNUfN1LSud8n
    
    E 2020-02-21T17:04:45.212375465Z Feb 21 17:04:45 - prevalidator.NetXdQprcVkpa.PsBabyM1eUXZ:  Request pushed on 2020-02-21T17:04:45-00:00, treated in 144us, completed in 44.523ms 
     
     E 2020-02-21T17:04:45.212384230Z Feb 21 17:04:45 - validator.chain: Update current head to BL1idjBg72ybk9DaD18StENbdT4cZb72E41VB9yiNUfN1LSud8n (fitness 01::000000000002bb1b), same branch
      
      E 2020-02-21T17:04:45.212390906Z Feb 21 17:04:45 - validator.chain: Request pushed on 2020-02-21T17:04:45-00:00, treated in 269us, completed in 52.379ms 
       
       E 2020-02-21T17:04:48.443273086Z Feb 21 17:04:48 - p2p.maintenance: Too few connections (1)
        
        E 2020-02-21T17:04:53.444052255Z Feb 21 17:04:53 - p2p.maintenance: Too few connections (1)
         
         E 2020-02-21T17:04:58.445291243Z Feb 21 17:04:58 - p2p.maintenance: Too few connections (1)
          
          E 2020-02-21T17:05:03.446054596Z Feb 21 17:05:03 - p2p.maintenance: Too few connections (1)
           
           E 2020-02-21T17:05:08.447218339Z Feb 21 17:05:08 - p2p.maintenance: Too few connections (1)
            
            E 2020-02-21T17:05:13.447877790Z Feb 21 17:05:13 - p2p.maintenance: Too few connections (1)
             
             E 2020-02-21T17:05:18.448312514Z Feb 21 17:05:18 - p2p.maintenance: Too few connections (1)
              
              E 2020-02-21T17:05:23.449632233Z Feb 21 17:05:23 - p2p.maintenance: Too few connections (1)
               
               E 2020-02-21T17:05:28.451053880Z Feb 21 17:05:28 - p2p.maintenance: Too few connections (1)
                
                E 2020-02-21T17:05:33.452308703Z Feb 21 17:05:33 - p2p.maintenance: Too few connections (1)
                 
                 E 2020-02-21T17:05:38.453098851Z Feb 21 17:05:38 - p2p.maintenance: Too few connections (1)
                  
                  E 2020-02-21T17:05:43.454016981Z Feb 21 17:05:43 - p2p.maintenance: Too few connections (1)
                   
                   nEqt4HgULBNukkmr8q successfully validated
                    
                    E 2020-02-21T17:05:43.713809298Z Feb 21 17:05:43 - validator.block: Request pushed on 2020-02-21T17:05:42-00:00, treated in 9.593us, completed in 1.215s 
                     
                     nEqt4HgULBNukkmr8q
                      
                      E 2020-02-21T17:05:43.781831967Z Feb 21 17:05:43 - prevalidator.NetXdQprcVkpa.PsBabyM1eUXZ:  Request pushed on 2020-02-21T17:05:43-00:00, treated in 246us, completed in 54.405ms 
                       
                       nEqt4HgULBNukkmr8q (fitness 01::000000000002bb1c), same branch
                        
                        E 2020-02-21T17:05:43.781847776Z Feb 21 17:05:43 - validator.chain: Request pushed on 2020-02-21T17:05:43-00:00, treated in 342us, completed in 61.991ms 
                         
                         E 2020-02-21T17:05:48.455394504Z Feb 21 17:05:48 - p2p.maintenance: Too few connections (1)
                          
                          E 2020-02-21T17:05:53.456781744Z Feb 21 17:05:53 - p2p.maintenance: Too few connections (1)
                           
                           ```

It appears that the public node had only one conncetion, but was still processing blocks. Presumably connection was from private node ?

Public node 1 is up and bootstrapped.

Private node appears running, but upon further inspection, it is 131 blocks behind.

Corrective action

Rekicking public node 1 fixes the issue.

