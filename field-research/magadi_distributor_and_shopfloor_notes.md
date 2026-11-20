# Field Research Notes: Magadi Town Distributor & Shop-Floor Observations

**Project:** Agentic Procurement & Quoting Co-Pilot for Small Manufacturers
**Hackathon:** Tech4Bharat 2026 — JanAI Mass Entrepreneurship Challenge (Theme 4, Problem 4.1)

---

## Research Background

| Field | Detail |
|---|---|
| **Primary Informant** | Wholesale and retail material distributor in **Magadi town, Karnataka** |
| **Experience** | ~20 years in the trade, serving both B2B (workshops, fabricators, contractors) and B2C (walk-in retail) buyers |
| **Additional Context** | Shop-floor observations of small manufacturing units (5–50 workers) managing daily raw-material buying and customer quoting |
| **Privacy** | All personal names, shop names, specific landmarks, phone numbers, and unique identifiers have been removed to protect participant privacy |
| **Research Method** | Informal structured interviews + direct observation during regular business hours |

---

## Key Lessons From the Ground

### 1. The "With-Bill vs. Without-Bill" Tax Trap

The distributor serves both retail walk-in customers (B2C) and registered factories and contractors (B2B). Every quote therefore naturally splits into two options:

- **With a GST bill (*pakka bill*)** — full price including 18% GST, supplier issues a proper tax invoice
- **Without a bill (cash rate)** — lower sticker price, no GST invoice, cash transaction

**What small factory owners get wrong:**

Many assume that buying "without a bill" saves them 18% upfront. This logic works for a retail consumer who cannot claim GST Input Tax Credit (ITC). For a **GST-registered factory**, it backfires:

- The factory loses the right to claim **Input Tax Credit (ITC)** on its purchase — meaning it must pay the full output GST on its sales invoices out of its own pocket.
- It loses **income-tax expense proof** — the unbilled purchase cannot be shown as a deductible business expense.
- It **cannot generate a valid E-Way bill** for any consignment worth over ₹50,000, making highway transport legally risky.
- If the factory's customer is a corporate buyer who demands a GST invoice, the factory must charge GST on its sale but has no ITC to offset it.

**What our system does:** Auto-computes the true net cost of "without-bill" vs. "with-bill" based on the owner's GST registration status and ITC eligibility, and surfaces the real rupee difference before any purchase decision.

---

### 2. Transport & *Hamali* (Unloading) Cost Distortion

Suppliers in this market quote in two fundamentally different ways, and buyers frequently compare them as if they are equivalent:

| Quote Type | What It Covers | What the Buyer Pays Extra |
|---|---|---|
| **Ex-Godown / Ex-Works** | Material only, at the supplier's warehouse door | Tempo hire + *hamali* (unloading labour) at destination |
| **Delivered (FOR — Freight On Road)** | Material + transport to factory gate, unloading sometimes included | Nothing extra (or small *hamali* charge) |

**Observed distortion:** Owners comparing raw per-kg or per-unit rates on WhatsApp routinely pick the lower Ex-Godown rate without adding tempo hire (₹1,500–₹3,500 per trip depending on distance and load) and *hamali* labour (₹400–₹1,200 per trip). A "cheaper" Ex-Godown supplier frequently ends up more expensive at the factory gate than a delivered-price supplier.

**What our system does:** Requires all quotes to be normalised to a **single Total Landed Cost at the factory gate** before comparison. The cost engine adds freight and *hamali* estimates (using owner's standard rates or supplier-declared transport cost) to any Ex-Godown quote automatically.

---

### 3. Panic Buying When Stock Hits Zero

Unlike experienced distributors who maintain reorder triggers and safety stock levels, small factory owners typically begin calling suppliers only when:

1. Raw material stock physically hits zero or near-zero on the shop floor, OR
2. An urgent customer order arrives that cannot be fulfilled with current stock

This panic mode drives two consistent bad outcomes:

- **Speed over price:** The owner accepts whichever supplier responds first with available stock, regardless of price or quality.
- **Skipping verification:** Under pressure, owners skip checking GSTIN validity, delivery terms, and whether the supplier has filed GSTR-3B in recent months (which affects E-Way bill eligibility).

**What our system does:** The Intake Agent runs a **Pre-Order Sanity Gate** before contacting any supplier — it checks physical vs. system stock, open POs and in-transit stock (which may already resolve the shortage), pending customer order deadlines, approved substitutes, and ABC-priority of the SKU (contribution margin per stockout day) — and presents this analysis to the owner before triggering outreach.

---

### 4. Direct Manufacturers vs. Local Wholesalers — When Each Wins

This trade-off is real and context-dependent. It is not always correct to prefer one over the other:

| Scenario | Preferred Source | Why |
|---|---|---|
| Large order quantity, 3–7 days lead time acceptable | **Direct manufacturer (mill)** | Lower base rate per kg/unit, Mill Test Certificates (MTCs) for grade verification, BIS marking where required |
| Urgent job, small quantity, same-day delivery needed | **Local wholesaler** | Maintains ready stock in smaller lots, faster delivery within the industrial cluster |
| Mill MOQ exceeds 90 days of safe demand | **Local wholesaler** | Locking up capital in excess inventory is costlier than the per-unit premium |
| Supplier's GSTIN is cancelled or filing is lapsed | **Neither — filtered out** | Invalid GSTIN means ITC is not claimable and E-Way bill cannot be generated |

**What our system does:** The Sourcing Agent runs a 3-wave waterfall — preferred direct mill first, rival direct mills second, local wholesalers third (and only if mill MOQ or ETA fails the owner's constraints). The Cost Engine then applies the same risk-adjusted ₹ formula to all options before comparison.

---

### 5. Credit Terms, Payment Safety & the Bank-Detail Change Risk

**Credit terms observed in the field:**

- Suppliers offer a small **cash discount for advance payment** (typically 0.5–1.5% of invoice value).
- Credit buyers pay a rate markup or face a **dispatch freeze** if any outstanding balance goes overdue.
- Some suppliers operate on strict **cash-and-carry** only for first-time or low-volume buyers.

**The bank-detail change fraud pattern:**

A known fraud tactic in informal trade involves a supplier (or someone impersonating a supplier via WhatsApp) messaging the buyer mid-transaction with a request to send payment to a "new" bank account or UPI ID due to some stated reason (account under audit, technical problem, etc.).

**What our system does:**
- Any change to a supplier's bank account number, IFSC code, or UPI handle triggers an **automatic hard freeze** on that supplier's quote.
- The owner is shown a fraud alert and must manually re-verify the new payment details through a separate out-of-band channel before the freeze lifts.
- WhatsApp messages from suppliers are treated as **untrusted input** — no message content can alter agent decision rules, change payment destinations, or trigger a purchase.

---

## Summary: Insight-to-System Mapping

| Field Insight | System Rule / Feature |
|---|---|
| "Without bill" looks cheaper but costs more for GST-registered buyers | `C_itc_loss` in the risk-adjusted cost formula; auto-computed ITC loss per supplier |
| Ex-Godown quotes miss freight + *hamali* | All quotes normalised to Total Landed Cost at factory gate before comparison |
| Panic buying leads to fastest-reply wins | Pre-Order Sanity Gate: stock check + open PO check before any supplier outreach |
| Direct mill cheaper on large orders; wholesaler better for urgent small orders | 3-Wave Waterfall (mill → rival mill → wholesaler), controlled by MOQ cap and ETA deadline |
| Lapsed GSTIN means no ITC and no E-Way bill | GSTIN validity + GSTR-3B 2-month filing check; auto-reject if failed and ITC needed |
| Bank-detail change messages in WhatsApp are a fraud vector | Hard freeze + fraud alert on any bank/UPI change; WhatsApp chat sandboxed as untrusted |
| Owners need speed but make costly decisions under pressure | Deal Review screen: top 2 options, exact rupee savings, reasons others filtered — one tap to approve |

---

*All notes are anonymized reconstructions for research purposes. No personal, business, or location details of the original informants are included.*
