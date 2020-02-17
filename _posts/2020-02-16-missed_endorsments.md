Signer is offline

From the endorsing daemon

```
│ Current head: BLWtBkJPfmud (timestamp: 2020-02-15T18:31:25-00:00, validation: 2020-02-15T18:35:58-00:00)                                                                                                         │
│ Bootstrapped.                                                                                                                                                                                                    │
│ Waiting for the node to be synchronized with its peers...                                                                                                                                                        │
│ Node synchronized.                                                                                                                                                                                               │
│ Endorser started.                                                                                                                                                                                                │
│ Feb 16 03:43:37 - 005-PsBabyM1.baking.endorsement: Error while injecting endorsement for delegate tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq :                                                                         │
│ Feb 16 03:43:37 - 005-PsBabyM1.baking.endorsement:   Error:                                                                                                                                                      │
│ Feb 16 03:43:37 - 005-PsBabyM1.baking.endorsement:     Unregistred error:                                                                                                                                        │
│ Feb 16 03:43:37 - 005-PsBabyM1.baking.endorsement:       { "kind": "generic",                                                                                                                                    │
│ Feb 16 03:43:37 - 005-PsBabyM1.baking.endorsement:         "error":                                                                                                                                              │
│ Feb 16 03:43:37 - 005-PsBabyM1.baking.endorsement:           "Found no ledger corresponding to ledger://redacted" }                                                             │
│ Feb 16 03:43:37 - 005-PsBabyM1.baking.endorsement:                                                                                                                                                               │
│ Feb 16 03:43:37 - 005-PsBabyM1.baking.endorsement:                                                                                                                                                               │
│ Feb 16 04:15:35 - 005-PsBabyM1.baking.endorsement: Error while injecting endorsement for delegate tz1gg5bjopPcr9agjamyu9BbXKLibNc2rbAq :                                                                         │
│ Feb 16 04:15:35 - 005-PsBabyM1.baking.endorsement:   Error:                                                                                                                                                      │
│ Feb 16 04:15:35 - 005-PsBabyM1.baking.endorsement:     Unregistred error:                                                                                                                                        │
│ Feb 16 04:15:35 - 005-PsBabyM1.baking.endorsement:       { "kind": "generic",                                                                                                                                    │
│ Feb 16 04:15:35 - 005-PsBabyM1.baking.endorsement:         "error":                                                                                                                                              │
│ Feb 16 04:15:35 - 005-PsBabyM1.baking.endorsement:           "Found no ledger corresponding to ledger://redacted." }                                                             │
│ Feb 16 04:15:35 - 005-PsBabyM1.baking.endorsement:                                                                                                                                                               │
│ Feb 16 04:15:35 - 005-PsBabyM1.baking.endorsement:                                                                                                                                                               │
│                                                      
```

ssh ing to the signers, we discover that the ledgers are not active.

This was due to a power outage at the colo where the signers were located. This power outage outlived the signer batteries (few hours) so the PINs had to be reentered and the baking app had to be restarted.
