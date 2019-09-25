---
layout: about
---

<script src="{{ base.url | prepend: site.url }}/assets/js/clipboard-polyfill.promise.js"></script>
We provide a fast, reliable and secure Tezos delegation service with a low intro fee and quick payouts.

## 1. Set your delegate to Hodl.farm

Use your wallet to set your delegate to the Hodl.farm address.

`tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq`  <a href="#!" onclick="clipboard.writeText('tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq');">⧉</a>
{: style="color:gray; font-size: 140%; text-align: center;"}

## 2. Wait for one cycle

**It takes only 4.5 days on average to get your first payout.**

A Tezos cycle lasts about three days. You need to delegate to us for **one full cycle** to get your first payout. Payouts then come regularly every three days until you stop delegating.

For example, if you delegate to us in the middle of cycle 475, your first payout will be at the end of cycle 476.

Most delegation services make you wait 5 cycles. Why wait two weeks ? **Use hodl.farm and get your payout faster !**

## 3. Check your contribution

Enter your Tezos KT address here: 

<input type="text" id="tezos_address" rows="800" placeholder="Your KT address">
<button onclick="window.location='payouts/'+getInputValue()+'.html';">Go!</button>

<style>
input[type="text"] {
    width: 500px;
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
