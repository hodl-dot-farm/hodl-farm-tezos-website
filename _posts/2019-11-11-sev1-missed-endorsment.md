---
layout: post
title: "Incident: missed endorsement"
date: "2019-11-08"
category: Incidents
---

## Incident report

As your delegate, we want to ensure that we execute our mission with diligence. We missed an endorsment on block [686032](https://tzstats.com/BLyvqwW8xQRHEHqcSiygfV3rjsbRoVhES9CwxMPKhUncXQ2rEgc) on November 9th.

While we insure for liveness, and as such, all our delegators will recieve their due whether or not we bake and endorse succesfully, we want to present here our investigation.

## Logs

Most of our setup in on Kubernetes, but the signers are on-prem.

Here is what can be seen in the endorser logs:

```
E 2019-11-09T05:49:49.665002992Z Nov  9 05:49:49 - 005-PsBabyM1.baking.endorsement: Error while injecting endorsement for delegate tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq : 
E 2019-11-09T05:49:49.665081162Z Nov  9 05:49:49 - 005-PsBabyM1.baking.endorsement: Error:
E 2019-11-09T05:49:49.665088280Z Nov  9 05:49:49 - 005-PsBabyM1.baking.endorsement: Unregistred error:
E 2019-11-09T05:49:49.665092936Z Nov  9 05:49:49 - 005-PsBabyM1.baking.endorsement: { "kind": "generic",
E 2019-11-09T05:49:49.665097602Z Nov  9 05:49:49 - 005-PsBabyM1.baking.endorsement: "error":
E 2019-11-09T05:49:49.665101880Z Nov  9 05:49:49 - 005-PsBabyM1.baking.endorsement: "Found no ledger corresponding to ledger://<redacted>/ed25519/<redacted>." }
E 2019-11-09T05:49:49.665117836Z Nov  9 05:49:49 - 005-PsBabyM1.baking.endorsement: 
E 2019-11-09T05:49:49.665122987Z Nov  9 05:49:49 - 005-PsBabyM1.baking.endorsement: 
```

Now, let's look at the on-prem signer logs:

```
Nov 09 05:49:48 raspberrypi tezos-signer[27309]: Nov  9 05:49:48 - client.signer: Request for signing 42 bytes of data for key tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq, magic byte = 02
Nov 09 05:49:48 raspberrypi tezos-signer[27309]: Nov  9 05:49:48 - client.signer: Signing data for key ledger_tezos
Nov 09 05:49:49 raspberrypi tezos-signer[27309]: Nov  9 05:49:49 - client.signer.ledger: WARNING: The device at [0001:0009:00] is not a Tezos application
Nov 09 05:49:49 raspberrypi tezos-signer[27309]: Nov  9 05:49:49 - client.signer.ledger: Transport level error:
```

The kernel logs have no information at that time.

Subsequent endorsment were successful, so we are facing a transient communication issue between the signer and the ledger.

## Attempt to reproduce

The app is [Tezos Baker](https://github.com/obsidiansystems/ledger-app-tezos) by Obsidian Systems, however we believe the issue is at lower level, with the USB driver.

The following command acts very similarly to a signature request: it connects to the ledger, verifies that the baking app is active, and checks that the baking key and path is what we expect. We run it in a loop every 10 seconds until something happens:

```
while tezos-signer get ledger authorized path for ledger_tezos; do sleep 5; done | ts | tee /tmp/ledgertest.txt
```

We believe that this exercises the system in the same way than actual signatures do, because it actually sends instructions to the app on the Ledger and waits for a response.

About 30 minutes later, we get the error:

```
Nov 11 19:07:39 - client.signer.ledger: WARNING: The device at [0001:000b:00] is not a Tezos application
Nov 11 19:07:39 - client.signer.ledger: Transport level error:
```

This time, the kernel logs (`dmesg`) show an USB disconnect/reconnect event:

```
[2651669.420397] usb 1-1.2: USB disconnect, device number 9
[2651877.592305] usb 1-1.2: new full-speed USB device number 10 using dwc_otg
[2651877.740888] usb 1-1.2: New USB device found, idVendor=2c97, idProduct=0001, bcdDevice= 2.00
[2651877.740904] usb 1-1.2: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[2651877.740914] usb 1-1.2: Product: Nano S
[2651877.740923] usb 1-1.2: Manufacturer: Ledger
[2651877.740932] usb 1-1.2: SerialNumber: 0001
[2651877.754761] hid-generic 0003:2C97:0001.0007: hiddev96,hidraw0: USB HID v1.11 Device [Ledger Nano S] on usb-3f980000.usb-1.2/input0
[2651877.764838] hid-generic 0003:2C97:0001.0008: hiddev97,hidraw1: USB HID v1.11 Device [Ledger Nano S] on usb-3f980000.usb-1.2/input1
[2651878.572505] usb 1-1.2: USB disconnect, device number 10
[2651878.872274] usb 1-1.2: new full-speed USB device number 11 using dwc_otg
[2651884.089241] usb 1-1.2: New USB device found, idVendor=2c97, idProduct=0001, bcdDevice= 2.00
[2651884.089252] usb 1-1.2: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[2651884.089257] usb 1-1.2: Product: Nano S
[2651884.089262] usb 1-1.2: Manufacturer: Ledger
[2651884.089267] usb 1-1.2: SerialNumber: 0001
[2651884.117736] hid-generic 0003:2C97:0001.0009: hiddev96,hidraw0: USB HID v1.11 Device [Ledger Nano S] on usb-3f980000.usb-1.2/input0
```

The Ledger itself does not reset: it stays online, and it remains in the signer app. I do not need to re-enter PIN. So it is not really disconnecting like the OS claims.

lsusb shows that the device number has incremented:

```
tezos@raspberrypi:~ $ lsusb
Bus 001 Device 005: ID 12d1:14dc Huawei Technologies Co., Ltd. E33372 LTE/UMTS/GSM HiLink Modem/Networkcard
Bus 001 Device 011: ID 2c97:0001
```

## Without LTE dongle

We reboot without the Huawei LTE dongle and redo the test.

After 2h30min, the loop exits because the tezos-signer command failed. We try it again:

```
tezos@raspberrypi:~ $  tezos-signer get ledger authorized path for ledger_tezos
Nov 11 22:56:44 - client.signer.ledger: WARNING: The device at [0001:0005:00] is not a Tezos application
Nov 11 22:56:44 - client.signer.ledger: Transport level error:
Error:
  Found no ledger corresponding to ledger://redacted with filter "App = Baking".
```

The `get ledger authorized path` command is normally snappy, but when I am in that state it can take a very long time for the command to return.

We observe the following in dmesg:

```
[13398.299231] usb 1-1.2: USB disconnect, device number 5
[13398.594779] usb 1-1.2: new full-speed USB device number 6 using dwc_otg
[13398.760734] usb 1-1.2: New USB device found, idVendor=2c97, idProduct=0001, bcdDevice= 2.00
[13398.760747] usb 1-1.2: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[13398.760757] usb 1-1.2: Product: Nano S
[13398.760766] usb 1-1.2: Manufacturer: Ledger
[13398.760776] usb 1-1.2: SerialNumber: 0001
[13398.778004] hid-generic 0003:2C97:0001.0004: hiddev96,hidraw0: USB HID v1.11 Device [Ledger Nano S] on usb-3f980000.usb-1.2/input0
```

We note that this message appeared after I ran the command manually one more time. When the loop exited, there was no disconnect yet.

## Without the UPS hat

We try again without the UPS hat just to rule out anything fishy going on with it.

Sure enough, the same thing happens after 2 hours 45 minutes.

## Ledger support

We opened a case with Ledger.

They advised to try:

```
- Disconnect and reconnect your device
- Use another USB2.0 port
- Change your cable to another USB2.0 data cable and the original Ledger cable
- Retry on another PC
```

I also ordered a Raspberry Pi 4 to see whether USB is more reliable than the current Raspberry Pi 3.

## Raspberry Pi 4

We ordered a Raspberry Pi 4, I just extracted and inserted the SD card into the new Pi, and everything just worked.

We connected the Ledger to the USB 3 port, then ran the same test:

```
while tezos-signer get ledger authorized path for ledger_tezos; do sleep 5; done | ts | tee /tmp/ledgertest.txt
```

It has been running now for 24 hours and has not failed.

## Finally

We ordered another Raspberry Pi 4 so both our signers will never loose connectivity to their Ledger again ! Thanks for reading.
