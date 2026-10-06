# Card format

```json
{
  "token": "USYC",
  "chain": "ethereum",
  "chain_id": 1,
  "contract": "0x136471a34f6ef19fE571EFFC1CA711fdb8E49f2b",
  "block": 25843230,
  "witnesses": [
    {"provider": "alchemy",   "totalSupply_raw": "…"},
    {"provider": "quicknode", "totalSupply_raw": "…"}
  ],
  "agree": true,
  "totalSupply": 54848168,
  "grade": "witnesses_agree",
  "registry_source": "<issuer official developer docs URL>",
  "unread_chains": ["solana", "sui", "canton"]
}
```

| Field | Rule |
|---|---|
| `block` | Both witnesses are pinned to this exact block |
| `totalSupply_raw` | Raw integer from the contract, before `decimals` scaling |
| `agree` | Bit-for-bit equality of the raw values |
| `grade` | From the confidence ladder; `dispute` publishes no figure |
| `unread_chains` | Always present, so a missing chain is never silently dropped |
