# Supply Chain Transparency App: MVP Spec

*Working document. Edit freely. Written so the whole team, or any AI assistant (Grok, Claude, ChatGPT), can pick up the project from here.*

Last updated: September 25, 2026

---

## 1. What we're building

An app where someone scans or enters a product and sees how its supply chain scores on forced labor risk, how confident that result is, and how transparent the company is. The long-term goal is full supply chain visibility for any product, in service of sustainable, human-rights-respecting sourcing.

**MVP focus:** apparel, starting with cotton t-shirts.

**Why apparel first:** well-documented chains, strong public trade data, active forced labor law (especially the Uyghur Forced Labor Prevention Act), and a product every user understands.

---

## 2. The team (7 people, 4 computers)

Roles for now. Replace with names once agreed.

| Role | Owns |
|---|---|
| Coordinator | Priorities, weekly check-in, funders and partners |
| Builder | Prototype and code, label reading tests |
| Investigator | Tracing companies and suppliers, verifying leads |
| Supply chain expert | Tier mapping, cost floor estimates, industry reality checks |
| Lawyer | Legal risk, wording, data licensing, right of reply |
| Human rights researcher | Labor and environmental risk data, wage benchmarks |
| Designer | User flow, screens, user testing |

---

## 3. MVP scope

**In the MVP**
- Enter a brand name or RN number (camera scanning comes later)
- Find the company's garment factories (Tier 1) from shipment data
- Trace at least one tier upstream (fabric and trims, Tier 2)
- Screen every supplier against banned-entity lists, sanctions, and known forced labor data
- Show three scores with a color system, plus a tier-by-tier supply chain view
- Every supplier-specific flag is human-verified before it's shown

**Not in the MVP (later)**
- Camera label scanning
- Industries beyond apparel
- Item-level results (MVP results are brand-level plus country)
- Physical fiber testing
- Public launch (MVP is for internal testing and demos)

**The MVP is done when:** we can enter 5 real brands and get a defensible, sourced result for each, and 5 test users understand what the result means.

---

## 4. User flow

1. **Enter or scan:** brand, RN number, country of origin, fiber content.
2. **Identify:** match the RN number or brand to a company.
3. **Trace:** find Tier 1 factories from shipment records, then go upstream tier by tier.
4. **Screen:** check each supplier against lists and risk data.
5. **Score:** calculate risk, confidence, and transparency.
6. **Show:** colored verdict, three scores, plain-language reasons, and the supply chain ladder.

---

## 5. The supply chain tiers (apparel)

| Tier | Stage | Typical visibility |
|---|---|---|
| 0 | Finished product and brand | High |
| 1 | Garment factory (cut and sew) | Medium to high |
| 2 | Fabric mills, dyeing, finishing, trims | Medium |
| 3 | Yarn spinning | Low |
| 4 | Raw cotton: ginning and farms | Very low, but highest forced labor risk |

Each tier is marked **Known** (traced in records), **Estimated** (inferred from trade patterns), or **Unknown**.

---

## 6. Scoring model (draft, needs team sign-off)

### 6.1 Three scores

- **Risk (0–100):** likelihood of forced labor in the chain. Uses the **worst tier**, not the average, because forced labor anywhere taints the product.
- **Confidence (0–100%):** how much of the result is verified versus estimated.
- **Transparency (0–100):** how far up the chain it's traced plus what the company discloses.

### 6.2 Forced labor risk indicator (per supplier or tier)

```
Cost Floor   = Materials + (Minimum Wage per hour × Labor Hours) + Overhead + Shipping
Price Ratio  = Declared Unit Price ÷ Cost Floor
Price Anomaly (0–100) = 0 if Price Ratio ≥ 1, else min(100, (1 − Price Ratio) ÷ 0.5 × 100)

Location Risk (0–100) = 60 if product-country is on the US DOL forced/child labor goods list
                        + 0 / 20 / 40 for low / medium / high regional prevalence (cap 100)

Entity Risk (0–100)   = 0 checked, no matches / 30 partly checked / 50 not checked / 90 watchlist or sanctions hit

Risk = 0.4 × Price Anomaly + 0.3 × Location Risk + 0.3 × Entity Risk
```

**Overrides:** a UFLPA Entity List match or an active CBP Withhold Release Order sets the result to **High** automatically.

**Draft thresholds:** Low below 35, Elevated 35–64, High 65 and up. *(Lawyer to confirm.)*

### 6.3 Unknown tiers inherit likely risk

When a tier can't be traced, estimate from trade flows:

```
Unknown Tier Risk = Σ (share of imports from each source country × that country's risk)
```

Replace with real data once the supplier is found.

### 6.4 Visibility (part of transparency)

Upstream tiers weigh more because they're harder to trace and riskier:
Tier 1 = 15%, Tier 2 = 20%, Tier 3 = 25%, Tier 4 = 40%.

### 6.5 Transparency scorecard categories

| Category | Weight |
|---|---|
| Traceability | 20% |
| Labor & human rights | 20% |
| Wage transparency | 15% |
| Environment | 15% |
| Legal & compliance | 15% |
| Company transparency | 15% |

Each category: `Score = Raw (0–100) × Confidence`, where confidence is 1.0 verified, 0.85 NGO or union report, 0.7 industry or country context, 0.5 proxy only, 0 unknown.

Overall score is capped at 20 if there's a High risk result or a confirmed sanctioned supplier.

### 6.6 Wage transparency

```
Wage Ratio = Estimated Wage ÷ Living Wage
Wage Score = 100 × min(1, Wage Ratio) × Confidence
Living Wage Gap per Product = (Living Wage/hr − Actual Wage/hr) × Labor Minutes ÷ 60
```

Score what companies **disclose**. Never imply a specific company underpays unless there's direct evidence.

---

## 7. Color system

| Color | Meaning |
|---|---|
| Green | Low risk, with enough data to say so |
| Amber | Elevated risk or open leads |
| Red | High risk or banned-list match |
| Grey | Not enough data to judge |

**Grey rule:** if confidence is below 40%, show grey regardless of the risk number. Unknown never looks safe.

---

## 8. Rules that protect users and the team

1. **Separate evidence from context.** "30 suppliers export goods on the DOL list for their country" is industry context. "This supplier is linked to a flagged entity" is supplier-specific and must be verified.
2. **Verify before naming.** No supplier-specific flag reaches users until a human confirms it's the right company and the source holds up.
3. **Call it a risk indicator, not proof.** Low prices can come from subsidies, dumping, or mislabeling.
4. **Never speculate on confidential data** (margins, contract prices, specific pay).
5. **Show sources and method** for every score.
6. **Right of reply:** companies can submit evidence to correct their result.
7. **Check data licensing** before showing any provider's data publicly.

---

## 9. Data sources and tools

| Source | Used for |
|---|---|
| Sayari | Company matching, shipments, upstream tracing, ownership, sanctions, UFLPA, Xinjiang-linked entities |
| Tradeverifyd | Trade and shipment data |
| Supply Trace (Northeastern / Sheffield Hallam) | Open apparel supply chain and Uyghur Region forced labor links |
| Open Supply Hub | Factory locations |
| US DOL goods list | Product-country forced and child labor risk |
| UFLPA Entity List, CBP Withhold Release Orders | Banned entities and products |
| FTC RN database | Label RN number to company |
| WageIndicator, Global Living Wage Coalition, ILO | Minimum and living wage benchmarks |

---

## 10. What we've learned so far

**Test trace (Sayari):** searched US customs records for cotton t-shirt shipments (HS 6109) since June 2025, picked one Vietnamese garment factory, and traced one tier upstream.

- 42 fabric and trim suppliers found, mostly linked to China and Hong Kong
- No sanctions matches
- 30 of 42 carry DOL goods-list flags (industry context, not company findings)
- 2 carry supplier-specific forced labor flags (unverified leads)
- Trace stopped at Tier 2; yarn and cotton not yet reached

**Takeaways:** the evidence-vs-context split is real in the data; automated tier-by-tier tracing is the core feature; verification must be built in.

---

## 11. Open problems and owners

| Feature | Status | Owner | Next step |
|---|---|---|---|
| Read label with camera | Simulated | Builder | Test on 5 label photos per person |
| Match label to company | Not solved | Investigator | Look up the team's RNs, record hit rate |
| Find factories (Tier 1) | Partly working | Investigator, Supply chain expert | Trace 5 brands, compare with published supplier lists |
| Trace yarn and cotton | Not solved | Supply chain expert, Investigator | Push 3 Tier 2 suppliers one tier further |
| Verify leads | Not solved | Investigator, Lawyer | Write checklist, verify the 2 test leads |
| Scoring rules and thresholds | Partly working | Lawyer, Human rights researcher | Agree thresholds, write methodology page |
| Wage data | Not solved | Human rights researcher | Wage table for top 10 garment countries |
| Price check (cost floor) | Not solved | Supply chain expert | Cost floor for a t-shirt in 3 countries |
| Licensing and legal risk | Not solved | Lawyer | Review data terms, draft right-of-reply policy |
| Brand vs item-level results | Not solved | Supply chain expert, Coordinator | List brands with product-level traceability |
| User testing | Not solved | Designer | Test prototype with 5 shoppers, 2 buyers |
| Live app and funding | Later | Builder, Coordinator | List 5 funders; sketch architecture |
| Physical cotton testing | Later | Coordinator | Revisit after paper trail works |

---

## 12. Using this document with Grok or another AI

Upload or paste this whole file at the start of a conversation, then ask for one specific task. Starter prompts:

- *"Read this MVP spec. I'm the [role]. Help me complete my next step in section 11."*
- *"Using the scoring model in section 6, write Python for Google Colab that takes supplier data and outputs the three scores and a color."*
- *"Stress-test the scoring rules in section 6. Give me 10 edge cases where the result would be misleading or unfair."*
- *"Draft interview questions to test whether shoppers understand the color system in section 7."*
- *"Draft a right-of-reply policy based on the rules in section 8, for our lawyer to review."*

**Tip:** don't paste raw data from paid providers (like Sayari) into other AI tools until the lawyer confirms the provider's terms allow it.

The clickable prototype is a single HTML file (`label-scan-prototype.html`). Any AI that can read files can edit it: upload it and describe the change you want.

---

## 13. Change log

| Date | Change | By |
|---|---|---|
| Sep 25, 2026 | First draft of MVP spec | Team |
