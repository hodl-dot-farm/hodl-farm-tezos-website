---
layout: about
---

## What is Tezos ?

Tezos is a cryptocurrency. Specifically, it is a [delegated proof-of-stake cryptocurrency](https://en.bitcoinwiki.org/wiki/DPoS).

[For more information, go to Tezos website](https://tezos.com).

## What is delegation ?

Delegation is like having your money in a savings account. By delegating to us, you help us bake and endorse more blocks. We earn rewards for that, and we pass them along to you - minus a small fee.

If your XTZ are on Coinbase, Kraken or any cryptocurrency exchange, you will not be able to delegate to us. To delegate, your assets must be in a wallet that you control.

Delegation is an operation on the blockchain that **does not change ownership** of your assets. When you delegate to us, you still own your coins ! Let that sink in: delegating is not the same as transfering. Please **do not send us your money** !

## Is delegation safe ?

In order to delegate, you must be in control of your XTZ address, which means that you own a private key which grants access to your assets. You must take precautions to protect your private key. Never send it to us, or anyone. We will never ask you for it.

The most common protection is to use a hardware wallet such as Ledger or Trezor, or an offline, air-gapped device (running for example the [AirGap app](https://airgap.it/)).

Being in control of your private keys is like being your own bank. It gives you great powers, which come with great responsibility. When in doubt, do not attempt to delegate to us, leave your assets in a cryptocurrency website such as Coinbase.

For further reading, we recommend Ledger's [best practices when using a hardware wallet](https://www.ledger.com/academy/hardwarewallet/best-practices-when-using-a-hardware-wallet/).

## What is the annual return on investment ?

In theory, the Tezos network inflation rate is 5.51%, as explained by the [Tezos official documentation](https://tezos.gitlab.io/whitedoc/proof_of_stake.html#inflation).

We are currently charing a 6.5% fee, which means that the theoretical yearly increase of your balance is (100 - 6.5) * 5.51% = **5.15%**.

Please note however that the actual return on investment is related to the amount of rewards that the network grants us at every cycle. These rewards depend on the behaviour of every baker, not just us.

We insure the liveliness of our own node but not the entire network. So the actual return on investment is lower than the nominal one.

Use our payout page to see the actual rate that we charge you at every cycle.

## How do payouts work ?

We send your rewards to you as soon as we can calculate them.

In the Tezos protocol, there is a grace period of two cycles from the moment when you delegate to us, to the moment when your delegation is taken into account by the protocol.

As soon as this grace period is over (7.5 days later on average) we are able to calculcate how many rewards you deserve. We then pay them to you **immediately**.

An interesting twist: since we pay rewards so early, we do not actually know what your fee is when the payment happens ! It takes us five extra cycles to know.

The baking and endorsing rights are calculated 5 cycles in advance (15 days). As soon as they are known, we calculate the payouts in optimal conditions - that is, if every baker and delegator is online and every transaction get broadcast perfectly. But the actual rewards depend on the behaviour of evey baker in ways that are impossible to predict until the blocks are actually baked and finalized.

Most bakers make you wait for the cycle to happen before they pay you, so they can accurately calculate the fee. We pay you based on an idealized fee which is **lower than the actual fee**.

We use the amazing [Backerei](https://github.com/cryptiumlabs/backerei) software from Cryptium Labs, a group very involved in Tezos development. You can trust that your payout amounts are accurate.

At hodl.farm, we insure liveness of our node and the whole network, so you can get the maximum payout that you deserve. Delegate to us today !

## Why are my payouts so irregular ?

We are a small baker. At the beginning of evey cycle, the network decides which slots we get to bake or endorse. These amount of slots are proportional to the number of rolls that we are responsible for. But there is a luck component.

You may see big swings from one cycle to the other (from 50% to 200% or more). But over a long period of time, it evens out.

Larger bakers have more consistent payouts because they have a large number of rolls. But rest assured that we pay you your fair share !

## How to delegate to you ?

There are many ways. Please check these excellent guides on Medium:

* [how to delegate with your Ledger Nano S](https://medium.com/cryptium/how-to-delegate-tezzies-tezos-xtz-with-your-ledger-nano-s-with-initial-setup-screenshots-519c9ae6654f)
* [how to delegate with the Airgap app](https://medium.com/airgap-it/airgap-how-to-delegate-tezos-86c392809535)

## How reliable are you ?

Our infrastructure resides on a distributed Google Kubernetes Engine cluster, with the exception of the signers, which are kept under our control.

We pride ourselves in being the one and only fully auditable cloud baker ! All of our infrastructure is declared as code and [open-source](https://github.com/hodl-dot-farm).

We have built several layers of redundancy in the system, we have performed significant reliability and security due diligence. Our utmost concern is that you get your rewards on time.

## How can I contact you ?

Please get in touch by email at hello@hodl.farm, Twitter [@hodl_farm](https://twitter.com/hodl_farm) or Telegram [t.me/hodl_farm](https://t.me/hodl_farm).

## Do you support the Babylon Tezos update ?

Yes. To our knowledge, we are the first Babylon-native baker.

## What are the terms of your Super Launch Discount ?

Here is the fee you will be getting by taking advantage of the Super Launch Discount:

* our first delegators have unlocked a **5% or 6.50% fee** until they stop delegating to us. This offer is no longer valid.
* right now our staking balance is at three rolls. If you delegate to us while our staking balance is under 5 rolls (40,000 XTZ), you will get a **8% fee** until you stop delegating to us

Any delegate after we reach 5 rolls will get our standard rate yet to be determined, and which may fluctuate over time.

Tezos baking and endorsing rights depends on how many "rolls" a baker has. A roll amounts to 8000 XTZ.

The more you delegate to us, the more rolls we have, and the more we can bake !

To determine which roll your delegation is a part of, we rely on the amount at the time of delegation based on the blockchain history. See our [delegation page on tezblock](https://mvp.tezblock.io/account/tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq).

If you delegation brings us over the threshold of 5 rolls (40,000 XTZ), then we will apply a rule of proportionality between the discounted 8% fee and the standard fee yet to be determined. The resulting fee will be lower than our standard rate, and may fluctuate over time. To lock in a fixed rate for life, **delegate now!**

The discounted rate only applies to the initial stake, and the natural increase of the stake over time. If you add more stake later to the same address, we reserve the right to change your rate.

Once we reach the limit of 5 rolls, we will post the resulting addresses and their associated fees as an update on our website.

The Super Launch Discount is subject to the [terms and conditions](terms-and-conditions.html), please read them carefully.
