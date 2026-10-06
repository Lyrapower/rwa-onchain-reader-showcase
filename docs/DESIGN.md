# Design

## Registry entry

```yaml
- token: USYC
  chain: ethereum
  chain_id: 1
  address: "0x136471a34f6ef19fE571EFFC1CA711fdb8E49f2b"
  nav_oracle: "0x74f2199AEb743f68f05943e5715A33EaF2b61f53"
  source: "<issuer official developer docs URL>"
- token: USYC
  chain: bsc
  chain_id: 56
  address: "0x8D0fA28f221eB5735BC71d3a0Da67EE5bC821311"
  source: "<issuer official developer docs URL>"
```

Rule: an entry without `source` is not loaded.

## Verification flow

```
registry entry
   │
   ├─ no address? ─────────────► REFUSE (never guess)
   │
   ├─ chainId check ───────────► mismatch → SKIP
   │
   ├─ pick block B (latest finalized from witness 1)
   │
   ├─ witness 1 @ B: symbol, decimals, totalSupply
   ├─ witness 2 @ B: symbol, decimals, totalSupply
   │
   ├─ symbol ≠ registry ───────► INVALID
   ├─ w1 ≠ w2 (bit-for-bit) ───► dispute
   ├─ w1 = w2 ─────────────────► witnesses_agree
   │
   └─ NAV: oracle answer / 10^oracle.decimals
           outside (0.5, 5) → reject NAV, keep supply
```

## Confidence ladder

| Grade | Meaning |
|---|---|
| `attested` | Issuer attestation matches the on-chain read |
| `sovereign_unwitnessed` | Read from our own node, with no commercial witness yet |
| `witnesses_agree` | Two independent commercial RPCs, same block, identical |
| `witness_only` | One commercial RPC only |
| `dispute` | Sources disagree, so no figure is published |

## Totals

Cross-chain total = sum of cards graded `witnesses_agree` or higher. A third-party aggregator figure is displayed next to it for reference and never added in.

## Not covered (stated on every card)

Non-EVM chains: Solana, Sui, Canton. Listed as unread.
