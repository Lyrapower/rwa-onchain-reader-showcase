# RWA On-Chain Reader — Showcase

A verifier for tokenized real-world assets (tokenized treasuries, money-market funds). It reads supply and NAV directly from the chain and publishes a figure only when independent sources agree on it. This repository is a **showcase**: design, rules and real output. The reader source is private.

## The problem

Dashboards report RWA totals, but a number with no block height, no source and no second witness cannot be checked. This reader works the other way round. Every figure carries the block it was read at, the contract it came from, the official document that names that contract, and a confidence grade.

## How it works

1. **Registry with provenance.** Each token entry names its contract address, chain and the issuer's official document that lists that address. If there is no official source, there is no entry.
2. **Direct reads.** JSON-RPC calls to the ERC-20 contract read `symbol`, `decimals` and `totalSupply`. NAV comes from the issuer's on-chain oracle, scaled by the oracle's own `decimals`.
3. **Self-checks at runtime.**
   - on-chain `symbol` ≠ registry symbol → the entry is invalid
   - `chainId` ≠ expected chain → skipped
   - no address → refuse to read; addresses are never guessed
   - NAV outside the sanity range (0.5, 5) → rejected
4. **Two witnesses, one block.** Two independent RPC providers are pinned to the **same block number**. The result is `verified` only if they match bit-for-bit.
5. **Confidence ladder.**

   ```
   attested  >  sovereign_unwitnessed  >  witnesses_agree  >  witness_only  >  dispute
   ```

   A self-hosted node is the *sovereign* evidence plane. Commercial RPCs are *witnesses*.
6. **Honest totals.** Cross-chain totals sum only cards where both witnesses agree. A third-party aggregator is shown alongside for reference and is never mixed into the total.
7. **Stated blind spots.** Chains the reader cannot read yet (Solana, Sui, Canton) are listed on the card as unread rather than left out.

## Real output: first run

| Field | Value |
|---|---|
| Token | USYC (Ethereum) |
| Contract | `0x136471a34f6ef19fE571EFFC1CA711fdb8E49f2b` |
| Block | 25843230 |
| Witnesses | Alchemy, QuickNode: **agree** |
| totalSupply | 54,848,168 USYC |
| Grade | `witnesses_agree` |

Registry also holds the USYC NAV oracle (Ethereum `0x74f2199AEb743f68f05943e5715A33EaF2b61f53`) and USYC on BSC (`0x8D0fA28f221eB5735BC71d3a0Da67EE5bC821311`). The source for all three addresses is the issuer's official developer documentation.

## Docs

- [docs/DESIGN.md](docs/DESIGN.md): registry schema, verification flow, confidence ladder
- [docs/CARD_FORMAT.md](docs/CARD_FORMAT.md): the published card, field by field

## Rights

See [NOTICE](NOTICE). Documentation shared for review. The reader source is not included and not licensed.
