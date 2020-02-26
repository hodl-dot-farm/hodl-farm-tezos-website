---
layout: post
title:  "Cycle 203 incident: double payout"
---

We have recently added a mechanism to prevent double payouts, but this mechanism failed once more :|

We run a payout cronjob every 3 hours. The first thing it does is check whether a payout has already been done from this cycle from the payout address to the witness address. These two addresses are set as terraform variables then passed on as kubernetes environment variables.

We had previously set the payout address as the same address than the witness address. That worked for a while, however something changed on tzstats and our query now returns an empty list !

We have now set the witness address to another delegate address that we control. Regular behaviour has been restored.

Commit does not reflect that but we have changed the comments as well:

https://github.com/hodl-dot-farm/tezos-auxiliary-cluster/commit/8c5e67285f0eba6bcce82b76da87dbec1b71b19b