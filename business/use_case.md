## Go-to-Market — First Paying Customer

Our first paying customer is a **DeFi protocol treasury manager** — specifically someone running a DAO treasury or protocol-owned liquidity position doing $500K+ in weekly swap volume. They can't afford to hire a quant team but they're losing 30-80 basis points per swap to MEV. At $0.01 per alert, protecting a $1M swap costs them $5 in intelligence. That's a 200x return if they avoid even one sandwich attack.

The first three customer segments in order:

1. **DEX aggregator protocols** — 1inch, ParaSwap, CowSwap. They route thousands of swaps daily and eat MEV losses on behalf of their users. One API integration, one Nitrolite channel, intelligence on every route decision.

2. **DeFi trading bots and MEV searchers** — counter-intuitively, searchers need to know what other searchers are doing. Our Hawkes process intensity tells them when a pool is being hunted. $0.01/query is trivial for a bot doing $100K/day in volume.

3. **Crypto hedge funds under $500M AUM** — too small for Bloomberg, too sophisticated for retail tools. They want VPIN and Kyle's Lambda but can't build it themselves.

---

## Unit Economics at 1 Million Queries/Day

```
Revenue:
1,000,000 queries/day × $0.01 = $10,000/day = $3.65M/year

Costs:
- Mempool node infrastructure:     $2,000/month
- Vertica compute (lambda-quad):   $500/month (owned hardware)
- AIsa.one/Anthropic API:          ~$0.001/analysis × 200K = $200/day
- Circle Arc gas fees:             ~$0.001/settlement × 2,000 = $2/day
- Nitrolite settlement overhead:   negligible (off-chain)

Total costs at 1M queries/day:    ~$8,000/month
Gross margin:                      ~97%
```

The model is extraordinarily capital-efficient because Nitrolite eliminates per-query gas costs. A traditional on-chain micropayment system at 1M queries/day would burn $10,000/day in gas alone. Our model burns $2/day in settlement gas.

At 10M queries/day — the infrastructure scales horizontally (add mempool nodes, add Vertica partitions) while the margin stays above 90%.

---

## Moat — Can Flashbots or EigenPhi Copy This in a Week?

No. Here's why:

**Data moat — 2.8M+ decoded blockchain events in Vertica.** We've been ingesting and decoding raw blockchain data since late 2025 using a zero-Etherscan dependency event signature database built from Sourcify and 4byte.directory. That's 6+ months of proprietary order flow data. Flashbots doesn't have this. EigenPhi has post-hoc data — we have pre-confirmation data.

**Model moat — VPIN calibrated on DeFi order flow.** VPIN was designed for equity markets. Applying it to DeFi requires rebucketing by block time, handling flash loan distortions, and accounting for sandwich bot volume which isn't toxic in the traditional sense — it's adversarial. That calibration took months. It's not something you replicate in a week.

**Payment moat — Nitrolite ERC-7824 + Circle Arc.** Flashbots has no micropayment infrastructure. EigenPhi has no payment layer at all — they're a data analytics company. Building a production payment channel network takes quarters, not weeks.

**Distribution moat — the x402 payment standard.** Once DEX aggregators integrate our Nitrolite payment endpoint, switching costs are high. They've already built the channel management, the settlement logic, the fallback handling. Ripping that out for a competitor means a full re-integration.

---

## Why Would a Hedge Fund Trust Our VPIN Over Their Own Quant Team?

They wouldn't — at first. And that's the right answer.

Hedge funds don't trust external signals blindly. What we offer is **a second opinion at $0.01 per query** — not a replacement for their quant team. Three reasons this works:

**1. We have data they can't easily get.** Real-time pre-confirmation mempool access with decoded calldata across multiple node providers is infrastructure-heavy. A $200M fund isn't going to build and maintain a multi-source mempool capture system for a problem they only partially understand. We've already built it.

**2. Our signals are academically grounded and auditable.** VPIN (Easley, López de Prado, O'Hara 2012), Kyle's Lambda (Kyle 1985), Hawkes processes — these are published, peer-reviewed models. A fund's quant team can audit our methodology, replicate our calculations, and validate our outputs against their own. That's a feature not a bug. Proprietary black boxes don't get past hedge fund due diligence. Open methodology does.

**3. Speed of deployment.** A fund's quant team is busy running their existing alpha. Building a VPIN pipeline on DeFi order flow is a 3-6 month project. Integrating our API takes a day. At $0.01/query they can run it in parallel with their own models, A/B test it, and shut it off if it doesn't add value. The risk is asymmetric — low cost to try, high value if it works.

The long-term play is not replacing their quant team — it's becoming their **DeFi microstructure data vendor**, the way FactSet or Bloomberg sells data to funds that have their own analysts.
