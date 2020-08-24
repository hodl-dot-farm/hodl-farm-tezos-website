---
layout: post
title: "Missed endorsements migrating to new wrapper script"
date: "2020-08-24"
category: Incidents
---

On 2020-08-24 we switched to the new tezos-signer-wrapper daemon that enhances the native daemon with health and status endpoint.

The goal is to implement better monitoring of the remote signer i.e. detecting power failures.

It is implemeted as follows: instead of forwarding tezos-signer endpoint directly, we built a flask app that uses python requests to forward the http incoming requests to the signer. Except the health and status endpoint which are intercepted by the script.

It worked well for GET requests, so we put it in production.

The first missed endorsement was due to the fact that we did not handle POST requests at all. The code was missing. That was fixed.

Then in order to test the POST request, I put the script in between local tezos-client and and the tezos-signer on my local machine, then verified that I was able to sign a transaction from a wallet to another that was managed by the signer.

However, it still did not work. When signing the next endorsement, this happened:

```
│ tezos-endorser-with-remote-signer-cust001 Aug 23 12:52:57 - 006-PsCARTHA.baking.endorsement:                                                                                                                   
│ tezos-endorser-with-remote-signer-cust001 Aug 23 12:52:57 - 006-PsCARTHA.baking.endorsement:                                                                                                                   
│ tezos-endorser-with-remote-signer-cust001 Aug 23 20:47:42 - 006-PsCARTHA.baking.endorsement: Error while injecting endorsement for delegate tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq :                             
│ tezos-endorser-with-remote-signer-cust001 Aug 23 20:47:42 - 006-PsCARTHA.baking.endorsement:   Error:                                                                                                          
│ tezos-endorser-with-remote-signer-cust001 Aug 23 20:47:42 - 006-PsCARTHA.baking.endorsement:     Rpc request failed:                                                                                           
│ tezos-endorser-with-remote-signer-cust001 Aug 23 20:47:42 - 006-PsCARTHA.baking.endorsement:        - meth: POST                                                                                               
│ tezos-endorser-with-remote-signer-cust001 Aug 23 20:47:42 - 006-PsCARTHA.baking.endorsement:        - uri: http://xtz-tezos-remote-signer-loadbalancer-cust001:8445/keys/tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq  
│ tezos-endorser-with-remote-signer-cust001 Aug 23 20:47:42 - 006-PsCARTHA.baking.endorsement:        - error: Oups! It looks like we forged an invalid HTTP request.                                            
│ tezos-endorser-with-remote-signer-cust001 Aug 23 20:47:42 - 006-PsCARTHA.baking.endorsement:                   <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">                                         
│ tezos-endorser-with-remote-signer-cust001 Aug 23 20:47:42 - 006-PsCARTHA.baking.endorsement: <title>400 Bad Request</title>                                                                                    
│ tezos-endorser-with-remote-signer-cust001 Aug 23 20:47:42 - 006-PsCARTHA.baking.endorsement: <h1>Bad Request</h1>                                                                                              
│ tezos-endorser-with-remote-signer-cust001 Aug 23 20:47:42 - 006-PsCARTHA.baking.endorsement: <p>The browser (or proxy) sent a request that this server could not understand.</p>                               
│ tezos-endorser-with-remote-signer-cust001 Aug 23 20:47:42 - 006-PsCARTHA.baking.endorsement:                                                                                                                   
```

This error `The browser (or proxy)` comes from flask, so it turns out, while tezos-client is able to send POST requests that flask can understand and forward, it is not the case for the endorser!

After both failures, we reverted to the old 1.0 baking cluster (which was still online).

We decided to intercept the traffic between the (old) endorser and the signer by using nc. Then, we get the raw HTTP request in a text file and we can replay it to the new script to see how we can avoid the error.

Here is the script:

```
root@raspberrypi:/home/tezos# cat /usr/bin/trafficSniffer
#!/bin/bash
ncat -lkv 0.0.0.0 8445 -c 'tee -a /var/log/trafficSniffer.log | ncat -v localhost 8443 | tee -a /var/log/trafficSniffer.log'

root@raspberrypi:/home/tezos# cat /etc/systemd/system/tezos-signer-sniffer.service
# The Tezos Signer sniffer service (for temporary debug purposes)
# To use, change the forwarder port to 8445

[Unit]
Description     = Tezos Signer Sniffer service
Wants           = network-online.target
After           = network-online.target

[Service]
ExecStart       = /usr/bin/trafficSniffer
Restart         = on-failure

[Install]
WantedBy        = multi-user.target

root@raspberrypi:/home/tezos#
```

Then we edit the tezos-signer-forwarder service to forward port 8445 instead of 8443, reload and restart. Load balancer is happy and still sees the signer as online.

All http traffic is dumped to the trafficSniffer.log file.

We see the keepalive check every 15 seconds:

```
root@raspberrypi:/home/tezos# tail -15f /var/log/trafficSniffer.log
Connection: close

HTTP/1.1 200 OK
content-length: 72
content-type: application/json

{"public_key":"edpkunnxQ7SD7pVorY4Gi1UWAS37R2vXogzU4MBQiofMqoyNYk2yYJ"}
GET /keys/tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq HTTP/1.0
Connection: close

HTTP/1.1 200 OK
content-length: 72
content-type: application/json

{"public_key":"edpkunnxQ7SD7pVorY4Gi1UWAS37R2vXogzU4MBQiofMqoyNYk2yYJ"}
```

We wait for next endorsement.

Here it is:

```
GET /authorized_keys HTTP/1.1
accept: application/json;q=1,application/bson;q=0.100,application/octet-stream;q=0.200
host: tezos-remote-signer:8445
user-agent: ocaml-cohttp/2.5.1
x-forwarded-for: 10.0.92.102

HTTP/1.1 200 OK
content-length: 3
content-type: application/json

{}
POST /keys/tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq HTTP/1.1
accept: application/json;q=1,application/bson;q=0.100,application/octet-stream;q=0.200
content-type: application/json
host: tezos-remote-signer:8445
transfer-encoding: chunked
user-agent: ocaml-cohttp/2.5.1
x-forwarded-for: 10.0.92.102

57
"027a06a7706fd6e65dff9b9be8170140041000010c16e"

0

HTTP/1.1 200 OK
content-length: 116
content-type: application/json

{"signature":"edsigthTtE3EbpPbd5VR8p5SaCR7GgYz7nRXf3SuKd8f"}
GET /keys/tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq HTTP/1.0
Connection: close

HTTP/1.1 200 OK
content-length: 72
content-type: application/json

{"public_key":"edpkunnxQ7SD7pVorY4Gi1UWAS37R2vXogzU4MBQiofMqoyNYk2yYJ"}

```

(redacted some parts of the signature)

We replay it to the flask app using nc locally. There are issues with encoded chunks, so we can't replicate the issue. Trying something else.

## try2

We set the baker address as coinbase, so we can have a lot of endorsement requests in a short period. They won't be signer, but since the bug seems to be at flask level, it's good enough.

We change the public key in the manifest. We restart the setup.

Predictably, the private node does not want to start because it can't get the remote signer.

I create a new folder on my remote signer ~/.tezos-signer-bak containing the fake coinbase address.

Then my command is:

```
 mv ~/.tezos-signer ~/.tezos-signer-prod && mv ~/.tezos-signer-bak/ ~/.tezos-signer
```

to switch to the fake signer and 

```
 mv ~/.tezos-signer ~/.tezos-signer-bak && mv ~/.tezos-signer-prod ~/.tezos-signer
```

to switch back to production.

After executing the first command, the load balancer on the old (currently in production) cluster fails. Ok.

After executing the second command, the load balancer on the old cluster comes back. Fine.

Run the first command again. Connect the signer to the new cluster.

Finally we get to replicate the issue:

```
│ tezos-endorser-with-remote-signer-cust001 Aug 24 15:08:30 - 006-PsCARTHA.baking.endorsement: Error while injecting endorsement for delegate tz1irJKkXS2DBWkU1NnmFQx1c1L7pbGg4yhk :                                   
│ tezos-endorser-with-remote-signer-cust001 Aug 24 15:08:30 - 006-PsCARTHA.baking.endorsement:   Error:                                                                                                                
│ tezos-endorser-with-remote-signer-cust001 Aug 24 15:08:30 - 006-PsCARTHA.baking.endorsement:     Rpc request failed:                                                                                                 
│ tezos-endorser-with-remote-signer-cust001 Aug 24 15:08:30 - 006-PsCARTHA.baking.endorsement:        - meth: POST                                                                                                     
│ tezos-endorser-with-remote-signer-cust001 Aug 24 15:08:30 - 006-PsCARTHA.baking.endorsement:        - uri: http://xtz-tezos-remote-signer-loadbalancer-cust001:8445/keys/tz1irJKkXS2DBWkU1NnmFQx1c1L7pbGg4yhk        
│ tezos-endorser-with-remote-signer-cust001 Aug 24 15:08:30 - 006-PsCARTHA.baking.endorsement:        - error: Oups! It looks like we forged an invalid HTTP request.                                                  
│ tezos-endorser-with-remote-signer-cust001 Aug 24 15:08:30 - 006-PsCARTHA.baking.endorsement:                   <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">                                               
│ tezos-endorser-with-remote-signer-cust001 Aug 24 15:08:30 - 006-PsCARTHA.baking.endorsement: <title>400 Bad Request</title>                                                                                          
│ tezos-endorser-with-remote-signer-cust001 Aug 24 15:08:30 - 006-PsCARTHA.baking.endorsement: <h1>Bad Request</h1>                                                                                                    
│ tezos-endorser-with-remote-signer-cust001 Aug 24 15:08:30 - 006-PsCARTHA.baking.endorsement: <p>The browser (or proxy) sent a request that this server could not understand.</p>                                     
│ tezos-endorser-with-remote-signer-cust001 Aug 24 15:08:30 - 006-PsCARTHA.baking.endorsement:     
```

After debugging we find the root cause and fix it:

https://github.com/openfaas-incubator/python-flask-template/issues/5

This was nasty. Testing locally with native flask, no issue, but in production with gunicorn/wsgi there was an issue :(
