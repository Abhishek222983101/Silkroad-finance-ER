# SilkRoad Finance - Ephemeral Rollups Edition

## Project Description & Ephemeral Rollup Usage
SilkRoad Finance is an institutional-grade, stateless Real World Asset (RWA) privacy layer on Solana that allows SMEs to tokenize and factor their invoices. In this upgrade, we leverage **MagicBlock's Private Ephemeral Rollups (PER/TEE)** to process high-frequency lending actions—like matching buyers to invoices, micro-repayments, and real-time yield accrual—in a completely gasless, sub-50ms environment. More importantly, using TEE-enabled Ephemeral Rollups ensures that sensitive financial positions and corporate supplier relationships remain strictly confidential. Opponents/Competitors cannot see your state.

## Any notes for us?
**Yes! We solved the "trust problem" that Jason and I chatted about.** By utilizing Private Ephemeral Rollups (PER/TEE) for state management, lenders and SMEs can interact without publicly exposing the terms of their agreements or their internal financial health. The TEE validator ensures that states (like an SME's debt position or a lender's available liquidity) are only readable by authorized parties, completely mitigating the risk of competitive front-running or corporate espionage while maintaining verifiable trust on-chain.

---

## 🎯 Winning the $700 Best Privacy Build
We are targeting the $700 first prize for the best privacy build. This requires the use of **Private Ephemeral Rollups (PER/TEE)**.

### Why PER/TEE is Critical for SilkRoad Finance
| Feature | Regular ER | Private ER (TEE) |
|---------|-----------|------------------|
| Speed | Sub-50ms | Sub-50ms |
| Gasless | ✅ | ✅ |
| Privacy | ❌ Competitors see your debt/liquidity | ✅ Competitors CANNOT see your state |
| Prize Eligibility | $400, $300 prizes | **$700 first prize** |
| Validator | `MUS3hc9TCw4cGC12vHNoYcCGzJG1txjgQLZWVoeNHNd` | `FnE6VJT5QNZdedZPnCoLsARgBwoE6DeJNjBs2H1gySXA` |
| Endpoint | `https://devnet-us.magicblock.app/` | `https://tee.magicblock.app/` |
| Auth Required | ❌ | ✅ Need to sign message for token |

---

## 🔧 Technical Architecture (PER-Focused)

Our architecture delegates the matching and execution of invoice factoring to a Private Ephemeral Rollup.

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                              FRONTEND                                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────────────────┐  │
│  │ Supplier    │  │ Investor    │  │         Factoring Marketplace       │  │
│  │ Dashboard   │  │ Dashboard   │  │  (Real-time matching, Hidden State) │  │
│  └─────────────┘  └─────────────┘  └─────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                        MAGICBLOCK TEE LAYER                                 │
│                                                                             │
│  ┌──────────────────────────────────────────────────────────────────────┐  │
│  │                    PRIVATE EPHEMERAL ROLLUP (PER)                     │  │
│  │                                                                       │  │
│  │   SME State               Deal State            Investor State       │  │
│  │   ┌─────────────┐         ┌─────────────┐       ┌─────────────┐      │  │
│  │   │ risk_score  │         │ amount      │       │ liquidity   │      │  │
│  │   │ invoices    │  ←──►   │ yield       │  ←──► │ portfolio   │      │  │
│  │   │ repayment   │         │ status      │       │ yield_gen   │      │  │
│  │   └─────────────┘         └─────────────┘       └─────────────┘      │  │
│  │         ↑                                                ↑           │  │
│  │         │         🔒 PRIVACY ENFORCED 🔒                 │           │  │
│  │    SME CANNOT read Investor's total liquidity            │           │  │
│  │    Investor CANNOT read SME's total debt outside deal    │           │  │
│  └──────────────────────────────────────────────────────────────────────┘  │
│                                                                             │
│  Endpoint: https://tee.magicblock.app/?token=${authToken}                  │
│  Validator: FnE6VJT5QNZdedZPnCoLsARgBwoE6DeJNjBs2H1gySXA                   │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼ (Commit & Undelegate on Settlement)
┌─────────────────────────────────────────────────────────────────────────────┐
│                           SOLANA L1 (Devnet)                                │
│                                                                             │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐            │
│  │ Invoice Minted  │  │ Final Transfer  │  │ ZK Compression  │            │
│  │ (Light Protocol)│  │ (Committed)     │  │ (State Hash)    │            │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘            │
└─────────────────────────────────────────────────────────────────────────────┘
```

## 🎮 Deal Flow (PER-Optimized)

1. **Connect & Auth:** Supplier and Investor connect wallets and get an Auth Token from `tee.magicblock.app`.
2. **State Delegation:** When an invoice is listed, the state is delegated from Solana L1 to the MagicBlock TEE validator.
3. **Privacy Permissions:** Using `create_player_permission` (adapted for businesses), only the specific Supplier and Investor involved in a deal can read the terms.
4. **Gasless Matching:** The Investor reviews the risk score (generated via Gemini AI) and funds the invoice inside the TEE instantly and without gas fees.
5. **Settlement & Undelegation:** Once the real-world fiat repayment clears (via Oracle/Admin trigger), the TEE state commits back to Solana L1, executing the final SOL transfer atomically and undelegating the accounts.

## Core Mandates Delivered
- **Stateless & Private:** Utilizing ZK Compression (Light Protocol) + MagicBlock TEE ensures no data leakage.
- **Solving the Trust Problem:** Lenders trust the AI risk score and the ZK proofs, while SMEs trust that their corporate relationships are hidden in the TEE layer.
