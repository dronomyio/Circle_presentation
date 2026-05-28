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
