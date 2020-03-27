---
layout: about
---

<script src="{{ base.url | prepend: site.url }}/assets/js/clipboard-polyfill.promise.js"></script>
We are a fast, reliable and secure Tezos delegation service with a **5% fee** and quick payouts.

<span style="color:#bf0000;font-weight:bold;">Open for delegations since Babylon !</span>

## 1. Set your delegate to Hodl.farm

Use your wallet to set your delegate to the Hodl.farm address.

`tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq`  <a href="#!" onclick="clipboard.writeText('tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq');">⧉</a>
{: style="color:gray; font-size: 140%; text-align: center;"}

Do not send funds to this address! [Learn more](faq.html#what-is-delegation-).

## 2. Wait

**It takes only 7.5 days on average to get your first payout.**

A Tezos cycle lasts about three days. You need to delegate to us for **two full cycles** to get your first payout. Payouts then come regularly every three days until you stop delegating.

For example, if you delegate to us in the middle of cycle 475, your first payout will be at the end of cycle 477. [Learn more](faq.html#how-do-payouts-work-).

Most delegation services make you wait 7 to 12 cycles. Why wait for weeks ? **Use hodl.farm and get your payout faster !**

## 3. Check your contribution

Enter your Tezos address here:

<input type="text" id="tezos_address" rows="800" placeholder="Your tezos address (starting with tz)">
<button onclick="window.location='payouts/'+getInputValue()+'.html';">Go!</button>

<style>
input[type="text"] {
    width: 80%;
    height: 25px;
}
button {
    width: 60px;
    height: 30px;
}
</style>

<script>
    function getInputValue(){
        // Selecting the input element and get its value 
        var inputVal = document.getElementById("tezos_address").value;
        
        // Displaying the value
        return inputVal;
    }
</script>

## Learn more

[Frequently asked questions](faq.html)

[See our account on tzstats](https://tzstats.com/tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq)

[Blog Posts](blog)