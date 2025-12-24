---
layout: post
title: "Quantum computers won’t kill Bitcoin — but they will clarify its role"
date: 2025-12-24
---

I was reading a recent Blockstream Research paper on post-quantum Bitcoin signatures. Much of it is deeply technical, but one detail immediately raised my eyebrows:

> Schnorr signatures: ~64 bytes  
> Post-quantum signatures (SPHINCS+): several kilobytes

That matters because Bitcoin fees are simple:

fee ≈ transaction size (vB) × feerate (sat/vB)


A very basic Bitcoin transaction today is ~150 vB.  
With post-quantum signatures, that same transaction becomes roughly 3,150 vB — about 20× larger.

Using conservative, “normal” conditions (BTC $70K, 20 sat/vB):

- Today: 3,000 sats ≈ $2  
- Post-quantum: 63,000 sats ≈ $44

At busier feerates or higher prices, this quickly reaches hundreds of dollars per on-chain transaction.

If BTC is $150K (arguably a fair price today), that becomes $100–$200 per on-chain settlement.  
If BTC is $1–3 million, the same math gives $2,000–$5,000 per transaction.

At that point, Bitcoin is no longer meant for casual payments on its base layer — and that’s not a failure.

**Bitcoin becomes a global settlement layer.**

Moving $1–10 million for a few thousand dollars is still vastly cheaper, faster, and more neutral than moving physical gold between banks or central bank vaults.

Everyday activity naturally moves to **Lightning**, where transactions remain cheap and instant. Only opening and closing channels touch the Bitcoin blockchain; the payments themselves stay off-chain and low-cost.

The same pressure applies to Ethereum. Larger post-quantum signatures increase data and verification costs, making **L2s essential, not optional**.

That’s why recent moves around Base matter, why Arbitrum will likely continue as a DeFi hub, and why Linea is interesting to watch given its Ethereum-native design and burn mechanics.

## One final thought

As Bitcoin matures into a credible reserve asset, it naturally becomes something **states will hold in their treasuries**, not just something markets speculate on.

Once that happens, incentives change in a fundamental way.

Whoever first gains meaningful quantum capability doesn’t just gain power — they also face the **largest bounty in history**. Exploiting Bitcoin would mean destabilizing an asset that governments, institutions, and allies increasingly rely on.

That creates a powerful counter-incentive: attacking the system harms your own balance sheet and undermines global trust.

In that environment, quantum capability stops being purely offensive and becomes **strategic infrastructure**.

The next “atomic bomb” isn’t a weapon in the traditional sense — it’s a quantum computer.  
And the winners won’t be those who use it recklessly, but those who integrate it into **alliances, standards, and resilient global systems**.
