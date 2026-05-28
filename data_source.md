An alert is generated when a pending mempool transaction matches **any of these conditions** in your MEV Shield scoring logic:

**Category filter** — transaction must be one of:
```
V3_SWAP, V2_SWAP, V3_MULTIHOP, UNIV_ROUTER, 
V3_ROUTER2, 1INCH, 1INCH_V5
```
OR any category with `cliff_score ≥ 0.30`

**Cliff score components** — the score combines:
- **VPIN** — Volume-synchronized probability of informed trading. High VPIN = toxic order flow
- **LOB depth** — Liquidity at current tick. Thin liquidity = easier to sandwich
- **Gas Z-score** — How far above median gas this tx is. High gas = bot-like urgency

**Specific signals that elevate cliff score:**
- `kyle_lambda > 0` — price impact per unit volume (informed trader signal)
- `hawkes_intensity > 0` — self-exciting clustering of similar transactions (sandwich setup)
- `nonce_gap = true` — gap in sender's nonce sequence (pending queue manipulation)
- `kyle_lambda = 1.0` — maximum score, fully informed order flow (USDT pool alerts you saw)

**The USDT alerts (`0xdac17f...`) you saw scored 0.52** because:
- Kyle's Lambda = 1.0 (maximum informed trader signal)
- ERC20 transfer on a high-volume pool
- Nonce gap detected

**What doesn't generate an alert:**
- Pure ETH transfers
- Contract deployments
- Low-value swaps below cliff threshold
- Already-seen tx hashes (deduplication via `seen` set)

- Good question. The current threshold of **0.30** is intentionally low — it was set for demo volume (lots of alerts = lots of payments). For a real production system the thresholds should be much higher.

**Why these categories qualify:**

These are all DEX router contracts — the only transaction types where MEV extraction is economically viable:

- **V2/V3_SWAP** — direct Uniswap swaps, the primary sandwich target
- **V3_MULTIHOP** — multi-hop routes, larger price impact, more profitable to front-run
- **UNIV_ROUTER / V3_ROUTER2** — Uniswap Universal Router, aggregates multiple operations
- **1INCH / 1INCH_V5** — aggregator swaps, often large size = high MEV value

Pure ETH transfers, NFT mints, contract deployments have no swap execution price to manipulate — so they're irrelevant.

---

**What the cliff score threshold should really be:**

| Threshold | Meaning | Use case |
|---|---|---|
| 0.30 (current) | Marginal risk, catches everything | Demo volume, research |
| 0.45 | Moderate risk | General trader alerts |
| 0.55 | High risk, sandwich probable | Production trading alerts |
| 0.65+ | Critical, near-certain extraction | Institutional, immediate action |

**Ideally for production:**
- Alert threshold: **0.50** — only flag genuinely dangerous transactions
- Analysis threshold: **0.45** — run Claude analysis on borderline cases
- Emergency threshold: **0.65+** — immediate private mempool routing recommendation

The USDT pool alerts you saw at 0.52 with Kyle's Lambda = 1.0 are actually **real high-risk signals** — those would qualify even at a strict 0.50 threshold. The V2_SWAP alerts at 0.30 are borderline and would be filtered out in production, reducing noise significantly.
