---
layout: post
title: "Cycle 203 incident: double payout"
date: "2019-02-26"
category: Incidents
---

We have recently added a mechanism to prevent double payouts, but this mechanism failed once more :|

We run a payout cronjob every 3 hours. The first thing it does is check whether a payout has already been done from this cycle from the payout address to the witness address. These two addresses are set as terraform variables then passed on as kubernetes environment variables.

We had previously set the payout address as the same address than the witness address. That worked for a while, however something changed on tzstats and our query now returns an empty list !

This is a secondary protection mechanism when the primary fails. The primary mechanism is backerei itself which is supposed to write to its internal database when the payout was done. In this case, it would appear that both mechanisms failed, resulting in our delegates being slightly richer than they ought to.

We have now set the witness address to another delegate address that we control. Regular behavior has been restored.

Commit does not reflect that but we have changed the comments as well:

https://github.com/hodl-dot-farm/tezos-auxiliary-cluster/commit/8c5e67285f0eba6bcce82b76da87dbec1b71b19b
