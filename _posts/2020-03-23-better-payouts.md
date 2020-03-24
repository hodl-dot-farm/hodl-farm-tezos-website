---
layout: post
title:  "2020-03-23 : Payout update"
---

Dear delegators,

When we launched our bakery in November 2019, we wanted to be different from the other bakers out there.

Back then, many big exchanges just announced that they were deploying staking. That was great news for Tezos, but also a bit worrying: the ecosystem thrives when a lot of small bakers are active. We were worried that the big exchanges would stifle decentralization of the network.

We looked at our options to make the value proposition of delegating a small, independent baker more compelling. We realized that we could pay delegators much earlier than what was (and still is) customary.

When a new cycle starts, the rights for baking and endorsing five cycles in the future are calculated. At this point, we can in theory calculate how much rewards we can distribute. So then, why not distribute them straight away ?

So that's what we did. But since then, we have been bleeding money ! The rewards for cycles that have not happened yet are calculated based on a best-case scenario, when every baker and endorser is online all the time.

In reality, that does not happen. Network glitches occur. Bakers get offline. Blocks get stolen. By design, the less optimally the network performs, the least rewards everyone gets.

Actual rewards are lower than expected rewards (or in the best case, equal). But by the time the actual rewards are known, you have already been paid ! Our nomimal fee is 5%, but effectively, you have been getting a lower fee than that. For some cycles, the fee has been negative even !

## New model

We have modified our rewards calculation so the extra money we distributed is withheld from a future payment.

When the cycle actually happens, we calculate the difference between what you have paid and what you should have paid. This difference is withheld from the payment for the future cycle.

### Example

Alice delegates 1000 XTZ to hodl.farm on cycle 300.

During two cycles, nothing happens. Then, at the beginning of cycle 303, Alice's stake is taken into account by the protocol for calculation of Hodl.farm's rights for cycle 308. We then immediately pay Alice her share of the future rewards associated with these rights. Let's say this amounts to 1.5 XTZ.

For cycle 304 to 308, the same thing happens. We pay Alice her share of the rewards for cycle 309 to 314.

Then, at the beginning of cycle 309, we calculate the rights for cycle 315. We also calculate the effective rewards for cycle 308 that have already completed.

Alice's rewards for cycle 315 are 1.1 XTZ. But for cycle 308, the finalized rewards for Alice were 1.4 XTZ, but we paid her 1.5 XTZ for cycle 308 ! We withhold the additional 0.1 XTZ from the cycle 315's rewards, so the resulting payout in cycle 309 is 1.0 XTZ !

And so on so forth. Every payout occuring during cycle N is the estimated rewards for cycle N+5 minus the debt for cycle N-1, until Alice stops delegating.

## Loophole
