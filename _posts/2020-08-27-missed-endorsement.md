---
layout: post
title: "Missed endorsements related to concurrency issues on the new wrapper script"
date: "2020-08-27"
category: Incidents
---

Missed endorsement. Error on the endorsing daemon:

```
│ tezos-endorser-with-remote-signer-cust001 Aug 27 10:38:45 - 006-PsCARTHA.baking.endorsement: Error while injecting endorsement for delegate tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq :                                                
│ tezos-endorser-with-remote-signer-cust001 Aug 27 10:38:45 - 006-PsCARTHA.baking.endorsement:   Error:                                            
│ tezos-endorser-with-remote-signer-cust001 Aug 27 10:38:45 - 006-PsCARTHA.baking.endorsement:     Found no ledger corresponding to ledger://<redacted>
│ tezos-endorser-with-remote-signer-cust001 Aug 27 10:38:45 - 006-PsCARTHA.baking.endorsement:                                                       
│ tezos-endorser-with-remote-signer-cust001 Aug 27 10:38:45 - 006-PsCARTHA.baking.endorsement:                                                      
│ tezos-endorser-with-remote-signer-cust001 Aug 27 10:52:29 - 006-PsCARTHA.baking.endorsement: Injected endorsement for block 'BKm5msEARxdM' (level 1102314, contract redacted): redacted
```

The next endorse was fine so it's an one-off error.

Remote signer wrapper script has nothing. Remote signer tezos daemon has the following:

```
Aug 27 11:38:44 raspberrypi tezos-signer[12939]: Aug 27 11:38:44.760 - client.signer: Request for public key tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq
Aug 27 11:38:44 raspberrypi tezos-signer[12939]: Aug 27 11:38:44.762 - client.signer: Found public key for hash tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq (name: ledger_tezos)
Aug 27 11:38:44 raspberrypi tezos-signer[12939]: Aug 27 11:38:44.958 - client.signer: Request for signing 42 bytes of data for key tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq, magic byte = 02
Aug 27 11:38:44 raspberrypi tezos-signer[12939]: Aug 27 11:38:44.959 - client.signer: Signing data for key ledger_tezos
```

We notice the heartbeat (which now also checks for ledger) happened 200ms before the signer request comes in.

Right after the signer check, we now do a ledger check (with a subprocess), so perhaps the ledger did not handle several concurrent requests well.

We checked /var/log/messages and dmesg, there were no USB disconnects.

Looking at the signer logs for other endorsements, we always see a time difference of at least 2 seconds between an heartbeat and the endorsement.

A solution is to make the signer-wrapper completely blocking.

Stack overflow looks promising:

https://stackoverflow.com/questions/42325105/flask-processing-requests-1-by-1

It would be sufficient to lock the sign and the heartbeat requests.

## Implementation

We implement a simple python lock with help from stack overflow

https://stackoverflow.com/questions/18213619/sharing-a-lock-between-gunicorn-workers

## Testing

We replicate the issue with the old script:

Here is a very simple test:

```
tezos@raspberrypi:~ $ cat /tmp/lock-test.sh
curl http://localhost:8443/statusz/tz1VoSM93UoY5gjuvb1bHdwdJZzU4P5eEAs4?ledger_url=ledger%3A%2F%2Feredactede%2Fed25519%2F0h%2F1h &
curl --header "Content-Type: application/json" -X POST -d '"027a06a7706fd6e65dff9b81"' http://localhost:8443/keys/redacted

tezos@raspberrypi:~ $ sh /tmp/lock-test.sh
{"public_key":"redacted"}
Error:
  Found no ledger corresponding to ledger://redacted/ed25519/0h/1h with filter "App = Baking".
[{"id":"signer.ledger","kind":"permanent","ledger-error":"Application level error (sign-with-hash): Parse error"}]
```

We write a new script with a lock mechanism. With the new script, we no longer see that. Problem solved?
