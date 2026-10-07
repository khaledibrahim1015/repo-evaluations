# Dub for MENA and Egypt: can it become a new product?

_Snapshot taken 2026-10-04 from a shallow clone of `dubinc/dub` (commit `ed064b9`, 2026-10-03) and the docs at dub.co/docs._

## TL;DR

1. **Yes, there's a real MENA idea here, but don't fork Dub to build it.** Dub's license splits in two. The part you could legally rebrand (short links, QR codes, click analytics) is the commodity part. The part that earns money (conversion tracking, affiliate and partner programs, commissions, payouts, fraud) is under a **commercial license** and can't be used in production without paying Dub.
2. **The AGPL part comes with strings.** If you run a modified copy as a SaaS, you must publish your full source code to your users. Competitors could then copy your MENA-specific work.
3. **Dub's model breaks in Egypt in three places:**
   - **Cash on delivery.** Dub pays commission when an order is placed, but in Egypt a large share of orders are refused or returned at the door.
   - **Payouts.** Dub pays partners through Stripe, PayPal and Tremendous. None of these reaches Egyptian influencers well; they want InstaPay, Vodafone Cash or a bank transfer.
   - **Platforms.** Dub integrates with Shopify and Stripe. MENA stores run on Salla, Zid, YouCan, EasyOrders and WooCommerce, and many take orders over WhatsApp.
4. **Recommended idea:** a **COD-aware influencer and affiliate tracking platform for MENA e-commerce**. Commission is paid only on *delivered* orders. It tracks coupon codes and WhatsApp orders, pays out to local wallets, and works in Arabic. Build it yourself and use Dub as the reference for how the product should work.
5. **It also fits Rawwejly.** Short links, QR codes and click-to-WhatsApp tracking are a small module in the Rawwejly PRD. The affiliate product could be Rawwejly's second product, or a stand-alone product sold to the same merchants.

---

## 1. What Dub is

| | |
|---|---|
| One-liner | Link attribution platform: short links → conversion tracking → affiliate programs |
| Scale (their claim) | 100M+ clicks and 2M+ links a month; customers include Twilio, Vercel, Perplexity |
| Stack | Next.js + TypeScript monorepo (Turborepo), Prisma on MySQL/PlanetScale, Tinybird analytics, Upstash Redis/QStash, Vercel, Stripe, Resend |
| Size | ~518k TypeScript lines; ~138k (about 27%) are in the commercial `(ee)` folders |
| Activity | Very high (commits daily) |
| Arabic / RTL | None found: no i18n library and no RTL handling in `apps/web` |

### Feature map by license

| Feature | Where in the repo | License | Can you sell it as your own SaaS? |
|---|---|---|---|
| Short links, custom domains, UTM builder, QR codes, link-in-bio basics | `apps/web/app/api/{links,domains,qr,utm}`, `lib/links` | AGPLv3 | Yes, but your whole server-side source must be public |
| Click analytics (country, device, referrer) | `app/api/analytics`, `lib/tinybird` | AGPLv3 | Yes, same AGPL condition |
| Conversion tracking (leads, sales, customers) | `app/(ee)/api/{track,customers,events}` | **Commercial** | **No**, not without a Dub Enterprise subscription |
| Affiliate / partner programs, commissions, payouts, bounties, fraud | `app/(ee)/api/{partners,programs,commissions,payouts,fraud,bounties}`, `partners.dub.co` | **Commercial** | **No** |
| Shopify discount-code attribution | `lib/integrations/shopify/attribute-via-discount-code.ts` (used by `(ee)` routes) | Mixed | Treat as **no** |

The README says the commercial part is "1%" of the code. By line count it is closer to a quarter, and it holds the features customers pay for.

### What the licenses mean in practice

- **`(ee)` Commercial License:** you may copy and modify it for development and testing only. Any changes you make belong to Dub. Production use needs a paid subscription.
- **AGPLv3:** you may fork, rebrand and charge money. Anyone who uses your hosted service over the network can ask for the complete source code of your modified version. Removing the `(ee)` folders is legal, but leaves only link shortening and analytics.
- **Infrastructure:** the self-hosting guide assumes Vercel, Tinybird, Upstash and PlanetScale. All are US services. That adds dollar costs, and it can be a problem for Saudi customers who need data kept in the country (Saudi PDPL). Replacing them (ClickHouse for Tinybird, Postgres/MySQL, BullMQ for QStash) is a large rewrite.

---

## 2. Why a straight "Dub for MENA" clone is weak

| Option | Problem |
|---|---|
| Rebrand the AGPL core as an Arabic short-link SaaS | Short links are a commodity: Bitly, Rebrandly, free shorteners and link-in-bio tools already exist. Arabic UI alone won't make people pay much, and AGPL means your code is public. |
| Rebrand all of Dub, including `(ee)` | Not allowed. It breaches the commercial license. |
| Pay Dub for Enterprise and resell | You'd pay US enterprise pricing to sell into a price-sensitive market, and still have no COD, local payouts or Salla/Zid support. |
| Use Dub's hosted product as a MENA agency | Possible as a service business, but it isn't a SaaS you own. |

---

## 3. The gap in MENA that Dub doesn't cover

Influencer and affiliate marketing is already how many MENA e-commerce brands sell, and today it is mostly tracked by hand: a coupon code per influencer, a spreadsheet, and a payout by bank transfer or wallet at the end of the month. Dub's model assumes things that don't hold here:

| Dub assumes | MENA reality | What your product does instead |
|---|---|---|
| Card payment at checkout, so a sale is a sale | Cash on delivery is common, especially in Egypt; many orders are refused or returned | Commission becomes **pending** on order, **approved** on delivery, **cancelled** on return; sync status from shipping companies (Bosta, Aramex, J&T, SMSA) |
| Stores on Shopify or Stripe | Salla, Zid (Saudi), YouCan (Morocco), EasyOrders (Egypt), WooCommerce, Shopify, and stores that take orders in WhatsApp chats | Native Salla, Zid and WooCommerce apps first; a WhatsApp order link that captures the influencer before opening the chat |
| Attribution via cookies and links | Instagram and TikTok users in the region often buy with a **coupon code**, not a link | Coupon code is the main attribution method; links and QR codes are secondary |
| Payouts via Stripe Connect / PayPal | Influencers want InstaPay, Vodafone Cash, Fawry, local bank transfer, or STC Pay in Saudi | Payouts through a local provider (Paymob payouts, or bulk wallet transfers), plus a downloadable payout file for manual transfer |
| English, USD, US tax forms | Arabic-first, EGP/SAR/AED, Saudi and UAE VAT invoices | RTL Arabic UI and partner portal, local currencies, local invoices |

**Existing regional players to study before building:** ArabClicks and Boostiny (influencer and coupon affiliate networks in the Gulf and Egypt), and Admitad (a global network active in MENA). They are mostly **networks** that take a cut of each sale. A **self-serve tool** where a brand runs its own program for a monthly fee is a different offer, and is closer to what Dub sells. Check how far each has gone into self-serve and COD before committing.

---

## 4. Recommended idea

### Working name: "Kodi" (placeholder; code + كودي, "my code")

**A self-serve tool where a MENA online store runs its own influencer and affiliate program: give each influencer a code and a link, see which ones bring *delivered* orders, and pay them to their wallet in one click.**

### MVP (about 8–12 weeks for one developer)

1. **Store connection:** Salla app, Zid app, WooCommerce plugin. Shopify comes later; Dub, Refersion and others already serve it.
2. **Partners:** invite influencers, generate a coupon code and short link for each, Arabic partner portal showing their sales and earnings.
3. **COD commission logic:** pending → approved on delivery → cancelled on return, with a hold period the brand chooses.
4. **Dashboard:** orders, delivered revenue, return rate and cost per delivered order, per influencer. Return rate per influencer is something brands can't get from a spreadsheet.
5. **Payouts:** monthly payout run with an exported file first; automatic wallet payouts once volume justifies it.

### Later

- Shipping-company sync to confirm delivery automatically
- A marketplace where influencers find brands (Dub is building this too: `app/(ee)/api/marketplace`, `network`)
- Fraud checks (self-orders, fake COD orders, coupon leaks to coupon sites)
- Put it inside Rawwejly as the "partners" tab

### Pricing hypothesis

Keep it simple and local, with no cut of sales to start, so you aren't seen as another network:

| Plan | Egypt | Gulf | Limits |
|---|---|---|---|
| Starter | EGP 900 / month | SAR 149 / month | 25 partners |
| Growth | EGP 2,500 / month | SAR 399 / month | 200 partners, automatic payouts |
| Agency | Custom | Custom | Many brands, white-label partner portal |

These numbers are guesses to test in interviews, not researched prices.

### Using Dub without breaking its license

- **Read it to learn the product:** data model (`apps/web/prisma`), the order of events (click → lead → sale), how commissions and payouts are shaped, and their public API docs. Ideas and product behaviour aren't protected by copyright; the code is.
- **Don't copy code** from the `(ee)` folders at all.
- **Copy AGPL code only if you accept publishing your server source.** Otherwise write your own. Short-link redirection is a small piece of code; it's not worth the AGPL obligation.
- **MIT parts:** `packages/embeds` has its own license file. Check it before reusing anything.

---

## 5. Risks

| Risk | Why it matters | Mitigation |
|---|---|---|
| Networks (ArabClicks, Boostiny) add self-serve | They already have the influencers and brand relationships | Win on COD accuracy, Salla/Zid depth and price; aim at brands too small for the networks |
| Salla and Zid build it in | Platforms often add popular app features | Support several platforms and shipping companies so you aren't tied to one; make it cross-store |
| Payout licensing | Moving money for third parties can need a payment license in Egypt and Saudi Arabia | Start by exporting payout files that the brand pays itself; use a licensed provider for automatic payouts |
| No marketing experience (your note) | You'll have to sell to brands yourself | The buyer is a small group (store owners and marketing managers), reachable through Salla/Zid app stores and Facebook groups for store owners |
| Dub expands to MENA | They have the product depth | They're unlikely to support COD, Arabic and local wallets soon; that is your moat |

## 6. Validation before building

1. Talk to 15 Egyptian and Saudi online stores that already pay influencers. Ask how they track sales today, what share of orders gets returned, and how they pay.
2. Talk to 10 micro-influencers. Ask how they get paid, how long they wait, and whether they trust the brand's numbers.
3. Pre-sell: build a clickable Arabic demo plus a Salla app listing draft, and aim for 5 paid pilots.
4. Build only when at least 3 brands say COD-aware commissions would save them real money or arguments.

---

## Appendix: sources read

- `dubinc/dub` at `ed064b9`: `LICENSE.md`, `apps/web/app/(ee)/LICENSE.md`, `README.md`, `apps/web/package.json`, `apps/web/app/(ee)/api/*`, `apps/web/lib/{integrations,payouts,partners}`
- dub.co/docs (self-hosting guide, API overview)
- `docs/PRD.md` in this repo (Rawwejly) for market and payment context (Paymob, Stripe availability in Egypt)
- Statements about the MENA market (COD share, regional networks, platform share) are from general knowledge and need checking during validation; they are not measured figures.
