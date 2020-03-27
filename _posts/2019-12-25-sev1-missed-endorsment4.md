---
layout: post
title: "Incident: missed endorsement"
date: "2019-12-15"
category: Incidents
---

Found private node with zero connections:

```
E 2019-12-25T19:01:37.823794687Z Dec 25 19:01:37 - p2p.maintenance: Too few connections (0)
E 2019-12-25T19:01:42.825054715Z Dec 25 19:01:42 - p2p.maintenance: Too few connections (0)
E 2019-12-25T19:01:47.825437688Z Dec 25 19:01:47 - p2p.maintenance: Too few connections (0)
E 2019-12-25T19:01:52.826615274Z Dec 25 19:01:52 - p2p.maintenance: Too few connections (0)
E 2019-12-25T19:01:57.827621825Z Dec 25 19:01:57 - p2p.maintenance: Too few connections (0)
E 2019-12-25T19:02:02.829049928Z Dec 25 19:02:02 - p2p.maintenance: Too few connections (0)
E 2019-12-25T19:02:07.829978452Z Dec 25 19:02:07 - p2p.maintenance: Too few connections (0)
E 2019-12-25T19:02:12.830475686Z Dec 25 19:02:12 - p2p.maintenance: Too few connections (0)
```

Seems to have been caused by an unschedulable public pod:

kubectl describe reveals:

```
Warning  FailedScheduling   3m17s (x1174 over 28h)  default-scheduler   0/2 nodes are available: 1 Insufficient cpu, 1 node(s) had volume node affinity conflict.
Normal   NotTriggerScaleUp  35s (x10412 over 28h)   cluster-autoscaler  pod didn't trigger scale-up (it wouldn't fit if a new node is added): 
```

It seems that the CPU usage of the nodes has gone up.

I put a CPU limit of zero on all containers of the cluster, and things fixed themselves. I found that one of the public nodes was very behind.

Another lesson of k8s for blockchains on the cheap: when the node is low on resources, the default behavior is to autoscale. Since we have disabled autoscaling, we must also disable CPU limits everywhere so the cluster will never put itself in a state where it should autoscale, but really stops working.
