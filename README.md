# PriceMitra: a price inside a floor–ceiling band

*Built by Team SponsHers for the Meesho DICE Challenge, Season 3 (Business Track).*

> **Problem statement:** Pricing across a product's lifecycle: the new-to-online seller.

**Live demo:** `https://github.com/Suvetha2006/Meesho_DICE_Price-Mitra` *(replace after enabling GitHub Pages)*

---

## The problem, in one story

Picture a kurta wholesaler from a smaller town. For years she has sold in a shop and a local market. She knows her cloth and her customers. Now she lists on Meesho, and the first question is: *what price do I put?*

Most new-to-online sellers guess. In our survey of vendors, **62.7% had "no clue"** how to price online, 21.6% wanted to sell online but didn't know how, and only 15.7% were already selling online.

So she does what feels natural:

- She carries over her offline price, and nothing sells.
- She copies the cheapest rival, and loses money on every order, because packaging, GST on freight, returns and ads were invisible offline.
- She prices too high, never gets discovered, and abandons the listing.

Online, the listed price is the whole negotiation. A wrong number costs a small seller real money.

## The idea

**PriceMitra gives every seller a safe price band and a suggested price inside it, in one tap.**

- **Floor:** the lowest price the seller can afford, built from her own true cost to serve.
- **Ceiling:** the most the market will bear, taken from what comparable live listings sell for.
- **Day-1 price:** a point between the two, chosen by how price-sensitive the category is.

The seller only provides what **only she knows**: her cost, her minimum margin, her launch ad budget. Meesho's AI layer fills in everything else from data Meesho already holds. She never has to know what "RTO" means.

If she skips every input, PriceMitra still gives her a sensible general price based on what other vendors in her category are selling for, and says it is a lower-confidence suggestion.

---

## How the price is calculated

### 1. Cost to Serve (what one order really costs her)

| Component | What it is | Who provides it |
|---|---|---|
| **COGS** | What she pays to acquire the product, including inbound freight | Seller (Meesho suggests the category median if she doesn't know) |
| **Packaging & handling** | Materials and time per order | Meesho category default, seller can override |
| **GST on forward logistics** | Meesho bears the freight; the seller pays only 18% GST on it | Meesho, from its rate card (weight and zone slab) |
| **RTO and return buffer** | Expected loss from failed deliveries and customer returns, spread across all orders | Meesho category data |
| **Launch ad spend** | One-time cost to get a new listing discovered, split across early orders | Seller's ad budget ÷ Meesho's expected early orders |

```
RTO and return buffer = (RTO rate × loss per RTO) + (return rate × loss per return)
Launch ad per order   = ad budget ÷ expected early orders      (dropped if the product already has traction)
Cost to Serve         = COGS + Packaging + GST on freight + RTO/return buffer + Launch ad
```

### 2. Floor

```
Floor (ex-GST) = Cost to Serve ÷ (1 − margin %)
Floor          = Floor (ex-GST) × (1 + GST rate)
```

GST is applied automatically: **5% up to ₹2,500, 18% above.**

### 3. Ceiling

```
Ceiling = median of 3–5 comparable live listings × seasonal index
```

The **seasonal index** is category demand in the period ÷ the annual average demand. Above 1.0 means peak season (for example, festive time), and below 1.0 means off-season.

### 4. Day-1 price

```
Day-1 price = Floor + k × (Ceiling − Floor)
```

**k** is the elasticity factor, from 0 (sit at the floor) to 1 (sit at the ceiling):

- **High-elasticity categories** (apparel, electronics): k ≈ 0.2–0.3. Price lower in the band to win conversion.
- **Low-elasticity categories** (gifting, differentiated items): k ≈ 0.5–0.7. Price higher in the band to protect margin.

### 5. The "no" is also an answer

If **Floor > Ceiling**, PriceMitra tells the seller **not to list yet**. At market price she would lose money on every order. It also shows how much she would need to cut her cost to make the product viable.

---

## A worked example: cotton kurta

These are the prototype's default demo values (all assumptions, see below).

| Step | Value |
|---|---|
| COGS | ₹220 |
| Packaging | ₹10 |
| GST on freight (18% × ₹50) | ₹9 |
| RTO and return buffer (17% × ₹120 + 8% × ₹100) | ≈ ₹28 |
| Launch ad (₹1,500 ÷ 100 orders) | ₹15 |
| **Cost to Serve** | **≈ ₹282** |
| Floor ex-GST at 15% margin (₹282 ÷ 0.85) | ≈ ₹332 |
| **Floor** (× 1.05 GST) | **≈ ₹349** |
| **Ceiling** (median of ₹420, ₹430, ₹440, ₹450, seasonal index 1.0) | **₹435** |
| **Day-1 price** (k = 0.25, rounded to end in 9) | **≈ ₹369** |

So the seller sees a safe range of **₹349 – ₹435**, and a suggested price of **₹369**. At that price she keeps roughly ₹50 per order after every cost and GST, instead of guessing.

---

## Why the Meesho AI layer is the real innovation

Without PriceMitra, a seller must answer questions she has never been taught:

> *"What is your RTO rate? What is your loss per return? What is the GST on your freight?"*

With PriceMitra, Meesho tells her instead:

> *"Women's kurtas in Tamil Nadu: RTO 17%, returns 8%, freight ₹50, ads ₹15 per order. Other vendors sell similar kurtas for ₹420–₹450. Suggested launch price: ₹369."*

**What this means for sellers**

- **No pricing literacy needed.** She provides what she knows, and Meesho fills the rest.
- **She sees costs that were invisible offline**, such as returns, GST on freight and launch ads.
- **No more copying the cheapest rival.** She sees a floor, so she knows where losing money starts.
- **No cold-start paralysis.** With no sales history, the suggestion uses category data and is clearly flagged as lower confidence.
- **She stays in control.** Every Meesho estimate can be overridden. The final accept or edit is always hers.

**What this means for Meesho**

- Fewer abandoned listings and fewer loss-making sellers.
- A more competitive catalogue with fairly priced products.
- A seller base that stays active and profitable.

Meesho is uniquely placed to do this. It already holds the true shipping and GST costs, category-level RTO patterns and live competitor prices that no individual seller can see.

---

## Using the prototype

Open `index.html` in a browser, or use the live demo link above.

**You provide (all optional):**
- Category, state and period (regular, festive peak, off-season)
- COGS, minimum margin, launch ad budget, weight
- A tick-box if the product already has traction (which drops launch ad spend)

**Meesho provides (shown in grey, tagged "Meesho"):**
- Packaging, freight, RTO rate, return rate, losses per RTO and per return, expected early orders, market median, seasonal index, and k

**Things to try:**
1. **Leave everything empty.** You get a general price for the category, a "Low confidence" tag, and the range other vendors are selling at.
2. **Type your own COGS and margin.** The price becomes personal to you, and the confidence goes up.
3. **Override a Meesho estimate**, for example the RTO rate. The tag switches from "Meesho" to "you".
4. **Switch to festive peak.** The ceiling moves up with the seasonal index.
5. **Raise the COGS** (try ₹400 for a kurta). Floor goes above Ceiling, and PriceMitra says *don't list*.
6. **Drag the price slider.** See what you keep per order and a Price Health badge: Underpriced, Healthy, Overpriced, or Below floor.

---

## Replacing the demo values with real Meesho data

This prototype runs entirely in the browser with **hand-written demo values**. We do not have access to Meesho's data, so every benchmark is an assumption chosen to make the logic visible. They are all stored in one place, at the top of the `<script>` section in `index.html`:

| Object | What it holds | Real source in production |
|---|---|---|
| `CAT` | Per category: median COGS, packaging default, RTO rate, return rate, loss per RTO and per return, ad budget, expected early orders, comparable listing prices, seasonal index, k | Meesho's category data, order history and live catalogue |
| `ST` | Per state: freight zone add-on and RTO adjustment | Meesho's rate card and regional RTO data |
| `slab()` | Freight by weight | Meesho's logistics rate card |

Swapping in real data means replacing these values with calls to Meesho's services. The formula and the screen do not change.

---

## Honest limitations

- **All benchmark numbers are demo assumptions.** This includes the 17% RTO rate, the 8% return rate, the ₹120 and ₹100 losses, the ad budgets, the comparable prices, the seasonal indexes, the k values and the freight slabs. They come from our deck and our own estimates, not from Meesho.
- **Comparable listings are hard-coded.** Live competitor tracking needs Meesho's catalogue data.
- **It does not yet learn from the seller's history.** The formula is designed to switch from category RTO data to the seller's own SKU-level rates as history builds. In this prototype, the seller can enter their own rates manually.
- **The lifecycle layer is not built yet.** The prototype covers the Day-1 price only. Repricing across launch, early signal, scale and decline is described in our deck.
- **GST on freight is simplified.** We apply 18% to a single freight figure and do not model input tax credit.
- **No backend, no real users.** It is a logic prototype and not evidence of adoption.

## What comes next

- Lifecycle repricing: triggers for the early-signal nudge, scale tuning and decline markdowns, all inside the same floor–ceiling band
- A catalogue view with Price Health badges and one-tap apply
- An Auto-Pilot mode with a hard floor so cuts can never go below cost
- Regional-language and voice input for sellers who are not English-first
- Success metric: **the seller's realized margin per order, sustained across the product's full life**

---

## Files in this repo

```
├── index.html     the working prototype (single file, no installation)
├── README.md      this file
└── docs/          our DICE deck (PDF)
```

## Run it locally

No build and no dependencies. Download `index.html` and open it in any modern browser. (It loads one Google Font, and works without it.)

## Team SponsHers

*Lakshmi*
*Purnima*
*Suvetha*

*Made for the Meesho DICE Challenge Season 3. This is a student prototype, not an official Meesho product. All figures are illustrative.*
