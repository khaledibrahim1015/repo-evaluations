# Egypt B2B fintech: problem analysis and opportunity for a unified API

Oct 7, 2026 · khaled

_Market facts come from 2026 press and regulator news, linked in Sources. Open-source license and activity were checked by cloning each repo on 2026-10-07. Statements marked **(verify)** are informed estimates that need a customer interview or a regulator answer before you rely on them._

## TL;DR

1. **Egyptian fintech has solved collecting money from consumers, and lending to consumers.** Paymob, Fawry, Kashier, Geidea, InstaPay and the wallets move money, and Valu, Lucky, MNT-Halan and others lend to consumers. **Money moving between businesses is still mostly cash, cheques and bank transfers with no reference**, followed by manual reconciliation in Excel.
2. **The gap is not another payment gateway. It is the layer above them**: matching each payment to an invoice, connecting e-invoice data with payment data and bank data, and handing a lender a clean picture of a business. Every fintech rebuilds this layer for itself, badly, and nobody sells it.
3. **Egypt has one asset most emerging markets lack: mandatory, structured, real-time B2B e-invoices.** Every VAT-registered B2B sale has gone through the Egyptian Tax Authority (ETA) since 2023, and from January 2026 the threshold fell to EGP 250k of turnover. That is a national, machine-readable ledger of who sells what to whom.
4. **Recommendation (updated, see section 10): lead with reconciliation for collections.** Payment-to-invoice matching is the daily, measurable pain of every Egyptian B2B seller. Sell a "cash application" product to the finance teams of distributors and B2B sellers first. The normalized invoice-plus-payment data it produces then becomes a second product: **"Codat/Belvo for Egypt"**, a business-data API for lenders such as factoring companies (EGP 132bn factored in 2025, up 78%), Valu's new SME arm and the banks' SME desks.
5. **Do not start by moving money.** Moving money needs a CBE PSP licence (rules in force since June 2025). Reading data with consent does not, and it gets you to revenue faster. Add a unified payments API (collections, payouts, reconciliation over Paymob/Fawry/Kashier/bank gateways) as phase 2, on top of the open-source **Hyperswitch** router. Hyperswitch has 161 connectors, **none of them for an Egyptian PSP**.
6. **Globally the pattern is proven**: Codat (UK) for SME data to lenders, Belvo and Syntage (Mexico) for tax-authority e-invoice (CFDI) data, India's GST + Account Aggregator stack, and Modern Treasury / Hyperswitch / Primer for payment orchestration and reconciliation.

---

## 1. How B2B money actually moves in Egypt

| Flow | Typical parties | How it is paid today | Online / offline |
|---|---|---|---|
| **FMCG distribution** | Manufacturer → distributor → wholesaler → hundreds of thousands of kiosks and groceries | Cash collected by the sales rep or driver, trade credit (آجل), post-dated cheques. B2B marketplaces (MaxAB-Wasoko, Capiter) add some in-app payment and credit | Mostly offline |
| **Industrial / SME suppliers** | Factories, traders, contractors | Bank transfer, InstaPay, cheques, 30–120-day terms | Mixed |
| **Services and SaaS** | Agencies, software, logistics, freelancers | Bank transfer, InstaPay to a personal account, card via Paymob/Kashier links | Online |
| **E-commerce sellers and their partners** | Merchants, couriers, fulfilment, marketplaces | Cash on delivery collected by the courier, then a weekly settlement to the merchant | Mixed |
| **Payroll and mass payouts** | Companies → staff, drivers, agents, gig workers | Bank payroll files, wallets, cash | Mixed |
| **Imports and FX** | Importers → foreign suppliers | Banks only; letters of credit and documentary collections | Offline, bank-led |

What changed in 2025–2026:

- **E-invoicing is universal for VAT B2B.** Each invoice is signed and sent to the ETA in real time, and gets a UUID before it is valid. From 1 Jan 2026 the registration threshold fell to EGP 250k, and late submission now carries graduated penalties up to a suspension of invoicing.
- **The FRA launched a digital factoring portal (Feb 2026)** with e-finance. Factoring companies can check against the Ministry of Finance and ETA whether an invoice has already been financed. Factored paper reached **EGP 132.2bn in 2025 (+77.8%)**.
- **Valu got an FRA SME-financing licence (May 2026)**, so a consumer-BNPL leader is moving into B2B lending. **Paymob raised $35m (Sept 2026)** and positions itself as a merchant financial-services platform. **MaxAB and Wasoko merged.**
- **The CBE's PSP/PSO licensing rules took effect on 17 June 2025**, which raises the bar for anyone moving money.
- **Open banking is still not mandated.** There is no PSD2-style right for a third party to access bank accounts. Banks (CIB, NBE, Banque Misr) expose APIs only through bilateral partnerships, and the CBE sandbox exists for testing.
- **InstaPay is the default rail between bank accounts** (instant, by phone number or `name@instapay`), but it is built for people. Businesses receive InstaPay transfers with no invoice reference and no webhook. **(verify:** the status of a merchant/business InstaPay product with request-to-pay and structured references in 2026**)**.

## 2. What fintechs already solve

| Problem | Who solves it | How well |
|---|---|---|
| Accepting card and wallet payments online | Paymob, Kashier, Fawry, Geidea, bank gateways (Mastercard MPGS at most banks) | Well. A crowded, commoditised market |
| Paying in cash at a retail outlet | Fawry, Aman, Masary, e-finance (Khales) | Well |
| Consumer instalments and BNPL | Valu, Contact, Souhoola, Sympl, Lucky, MNT-Halan | Well, also crowded |
| POS and soft-POS for small shops | Geidea, Paymob, banks | Getting there |
| Credit for small retailers on a marketplace | MaxAB-Wasoko, Capiter, Fawry, Halan | Only inside their own network, using their own order data |
| E-invoice issuing (compliance) | ERP vendors and ETA integrators (Odoo localisations, local accounting software, consultancies) | Done, but as a compliance chore. The data is not reused |
| Factoring | Banks, about 20+ FRA-licensed factoring companies | Growing fast, but onboarding and underwriting are manual |

## 3. The unsolved problems

These are the problems nobody in Egypt sells a solution for as infrastructure today. Each one is something every fintech, lender or large distributor rebuilds internally.

### 3.1 Reconciliation: "Who paid, and for which invoice?" (the core problem; see section 10)
A distributor receives money through 4–6 channels: cash with reps, InstaPay to its bank account, bank transfers, Fawry/Paymob links, cheques and courier COD settlements. **None of them carry a reliable invoice reference.** The finance team matches bank statements to invoices by hand. That costs days of delay, unapplied cash, and disputes with customers.
- **Why fintechs don't solve it:** each PSP reconciles only its own transactions. Bank statements are not available through an API without a bilateral deal. InstaPay has no business-grade remittance data.

### 3.2 Fragmentation: each provider has its own API, settlement file and report
Paymob, Fawry, Kashier, Geidea, every bank's MPGS gateway and every wallet use different APIs, webhook formats, settlement timings, fee structures and refund flows. A company using two or three of them, which is common for redundancy and coverage, has to integrate and reconcile each one separately.
- **Evidence:** Hyperswitch, the leading open-source payment orchestrator, has **161 connectors and none for Paymob, Fawry or Kashier** (checked 2026-10-07; it does include Mastercard MPGS, Noon and Fiserv EMEA). Global orchestrators skip Egypt.

### 3.3 The invoice, the payment and the financing live in separate worlds
The ETA invoice (with its UUID), the payment that settles it, and the factoring or loan that finances it are not linked anywhere. The FRA portal only checks for double financing. Nobody can answer "this invoice was issued, accepted by the buyer, is 45 days old and has been 60% paid" through an API.

### 3.4 Lenders can't see SMEs' cash flow
- No open-banking account-data API, so no bank-statement-based underwriting at scale.
- The data that exists is scattered: ETA invoices, accounting software, POS and PSP volumes, marketplace orders, i-Score bureau data, which has thin SME coverage.
- So SME credit goes to whoever already holds the data (marketplaces lend to their own retailers, Valu to its own merchants), and everyone else is underwritten from PDFs and paper. **This is the most urgent pain, because the lenders have money and new licences now.**

### 3.5 Offline cash collection in distribution
Reps and drivers collect cash and cheques. That leads to leakage, fraud, delays in depositing cash, and no real-time view of receivables. Converting this to digital needs a collection tool that works offline, issues a receipt, and posts to the ERP. It is not a payment gateway problem.

### 3.6 Payouts to many recipients across banks and wallets
Paying suppliers, couriers, agents and freelancers across banks, wallets and InstaPay addresses means bank payroll files, manual uploads and failed transfers that are hard to track. There is no single "pay this list" API with status tracking. **(verify** the current coverage of Paymob/Fawry disbursement products**)**.

### 3.7 Integration with ERPs and accounting software
Egyptian SMEs use Odoo, ERPNext, local accounting tools and Excel. Every fintech that wants to offer financing or payments inside the business's workflow builds its own ERP connectors, if it builds them at all.

## 4. Why the existing fintechs have not solved these

- **Their business model points elsewhere.** PSPs earn per transaction and lenders earn interest. Reconciliation and data normalisation are cost centres to them, not products.
- **Each is vertically integrated.** Paymob wants you on Paymob and Fawry wants you on Fawry. A neutral layer across competitors has to be built by a third party.
- **Regulation.** Without an open-banking mandate, bank data needs bilateral contracts, and moving money needs a PSP licence. Both favour incumbents, but they leave room for a data-and-software layer that reads with consent and does not hold funds.
- **B2B is messy.** Credit terms, partial payments, returns, credit notes and cheques make B2B much harder than B2C acceptance, so most fintechs stay with B2C.

## 5. Opportunities, ranked

| # | Opportunity | Buyer | Licence needed | Time to first revenue | Verdict |
|---|---|---|---|---|---|
| **A** | **Unified business-data API**: ETA e-invoices + ERP/accounting + PSP data, normalised, with consent | Factoring companies, SME lenders (Valu SME, banks, Lucky, Halan), insurers, B2B marketplaces | None to read data with consent; Personal Data Protection Law 151/2020 compliance (verify licensing for data controllers) | **Short** | **Start here** |
| **B** | **Receivables and reconciliation platform** for distributors and mid-size companies: invoice → payment links → auto-match → collections for reps | CFOs of distributors, manufacturers, logistics and e-commerce companies | None if funds go straight to the merchant's own PSP or bank account | Medium | **Build on A; this is the customer-facing product** |
| **C** | **Unified payments API**: one API for Paymob, Fawry, Kashier, Geidea and bank MPGS, plus payouts | SaaS platforms, marketplaces, enterprises using several PSPs | None if you only route to the merchant's own PSP accounts (software, like Hyperswitch); PSP licence if you hold funds | Medium–long | **Phase 2** |
| D | Invoice-financing marketplace (match ETA invoices with factoring companies) | SMEs + factoring companies | FRA rules; broker or partner model | Long | Later, once A and B give you the data and the distribution |
| E | Open-banking account-data aggregator | Lenders | Bilateral bank deals, CBE sandbox | Long, regulator-dependent | Wait for the CBE; Lean already has a Cairo office |

### Why A first
- **The data exists and is standardised by law.** The ETA exposes a documented REST API for ERP systems (submit, search and get documents, including invoices the business *received*). With the taxpayer's consent and their ERP-system credentials, you can pull a business's full sales and purchase history. **(verify** with a tax lawyer whether a third party may use ERP credentials the taxpayer created for this purpose, and the ETA's terms on it**)**.
- **The buyers have money and urgency right now.** Factoring grew 78% in 2025, the FRA is digitising it, Valu just got an SME licence, and banks are pushed to grow SME portfolios.
- **It is defensible.** Normalising Egyptian invoices, credit notes, cheques, Arabic names and tax IDs across sources is unglamorous work that compounds. Every new connector and every consented business makes the network more valuable.
- **It feeds B and C.** Once you know a business's invoices, matching payments to them (B) is the next obvious step, and routing those payments (C) after that.

### What the MVP looks like
1. **Consent and connect flow** (web widget, like Plaid Link or Codat Link): the SME logs in, consents, and connects ETA (ERP credentials), its accounting tool and one PSP.
2. **Normalised endpoints:** `/companies`, `/invoices` (issued and received, with ETA UUID, status, buyer, VAT), `/credit-notes`, `/payments` (from the PSP), `/receivables-aging`, `/top-customers`, `/top-suppliers`.
3. **Lender reports:** monthly sales, customer concentration, invoice ageing, the share of invoices cancelled or rejected, and a simple risk flag. A PDF plus JSON.
4. **Invoice verification endpoint** for factoring: is this invoice valid in the ETA, was it accepted, is it cancelled? This complements the FRA double-financing check.
5. **Webhooks** when new invoices arrive.

## 6. Who solves this globally

| Layer | Global players | What to learn |
|---|---|---|
| SME data for lenders (accounting, commerce, banking) | **Codat** (UK), **Rutter**, **Railz** (acquired by FIS), **Validis** | The product is the consent flow, normalised data model and lender-ready reports. Codat sells to banks such as Lloyds and to fintech lenders |
| Unified accounting/ERP APIs | **Merge**, **Apideck**, **Chift** (Europe) | One data model for many ERPs; how they handle sync, rate limits and webhooks |
| **Tax-authority e-invoice data** (the closest analogue to Egypt) | **Belvo** and **Syntage** (Mexico, SAT CFDI data), Brazilian NF-e data providers, **India: GSTN APIs + Account Aggregator + OCEN** (GST-data lending through Perfios, Setu, FlexiLoans and others) | **Mexico and India show that e-invoice data becomes the main SME underwriting source once it is accessible.** Egypt's ETA is the same kind of asset |
| Open banking aggregation | Plaid, TrueLayer, Tink (Visa); in MENA **Lean** (first licensed provider in Saudi Arabia, March 2026, with a Cairo office) and **Tarabut**; in Africa Mono and Stitch (Okra shut down in 2025) | Infrastructure-only plays need a mandate or the banks' cooperation. In Egypt, stay out until the CBE acts |
| Payment orchestration | **Hyperswitch** (Juspay), Primer, Spreedly, Gr4vy | Routing, retries, a common data model across PSPs, and reconciliation as a feature |
| Money movement and reconciliation for businesses | **Modern Treasury**, Moov, Increase | A ledger plus payment orders plus auto-reconciliation against bank statements |
| AR automation and B2B payments | Billtrust, Versapay, Upflow, Chaser; supply-chain finance: Taulia (SAP), C2FO | Collections workflow, customer portal, invoice-to-cash |

## 7. Open-source building blocks

Checked 2026-10-07 (license file and last commit on the default branch).

| Project | What it gives you | License | Last commit | How to use it |
|---|---|---|---|---|
| [Hyperswitch](https://github.com/juspay/hyperswitch) | Payment orchestrator in Rust: unified API, routing, retries, vault, reconciliation module | Apache-2.0 | 2026-10-07 | **Base for phase C.** Write Paymob, Fawry and Kashier connectors; you can contribute them upstream as a moat and for distribution |
| [Blnk](https://github.com/blnkfinance/blnk) | Ledger + **reconciliation engine** + balance monitoring (Go) | Apache-2.0 | 2026-10-05 | **Base for B**: matching rules between invoices, payments and bank lines |
| [Formance Ledger](https://github.com/formancehq/ledger) | Programmable double-entry ledger (Go) | MIT | 2026-10-05 | Alternative ledger. The wider Formance stack also has a payments connector layer |
| [TigerBeetle](https://github.com/tigerbeetle/tigerbeetle) | High-throughput financial transactions database | Apache-2.0 | 2026-10-06 | Only when volume calls for it; overkill for an MVP |
| [Apache Fineract](https://github.com/apache/fineract) | Core lending and savings (loans, repayment schedules) | Apache-2.0 | 2026-10-07 | If you later offer lending-as-a-service or invoice financing |
| [Lago](https://github.com/getlago/lago) | Usage-based billing | **AGPL-3.0** | 2026-10-07 | To bill your own API customers per call or per connected company. Run it unmodified as a separate service; don't embed its code |
| [Midaz](https://github.com/LerianStudio/midaz) | Ledger for building core banking | **Elastic License 2.0** (not open source: you may not offer it as a managed service) | 2026-10-07 | Study only |
| [OBP-API](https://github.com/OpenBankProject/OBP-API) | Open banking API gateway, used by banks and regulators | **AGPL-3.0** | 2026-10-06 | Reference for an open-banking data model; useful if you do bank integrations later |
| [Moov Watchman](https://github.com/moov-io/watchman) | Sanctions and watchlist screening | Apache-2.0 | 2026-10-07 | KYB/AML checks on counterparties |
| [ERPNext](https://github.com/frappe/erpnext) / Odoo | ERPs used by Egyptian SMEs, with ETA e-invoice localisation modules from the community | GPL-3.0 / LGPL-3.0 (Odoo Community) | 2026-10-07 | **Data sources to connect to**, and a good place to read how ETA signing and submission are implemented |
| [Temporal](https://github.com/temporalio/temporal) | Durable workflows (sync jobs, retries) | MIT | n/a | Run connector syncs and webhooks reliably |

Licensing rules, the same as in the earlier plans: Apache/MIT code can be reused with attribution. Run AGPL services unmodified and separately, or not at all. Elastic License code is study-only for a hosted product.

## 8. Risks and open questions

- **ETA access model** (the biggest risk): can a third party pull invoices using credentials the taxpayer created, at scale, and will the ETA tolerate it? Mitigation: talk to the ETA and to an ETA-accredited integrator early. A partnership with an ERP vendor or integrator gives a second path.
- **Data protection:** Law 151/2020 on personal data and its executive regulations. B2B invoice data is mostly company data, but it includes sole proprietors. Build consent, audit logs and data minimisation from day one.
- **Lenders build it themselves:** large banks may. Your edge is neutrality, speed, and serving the 20+ factoring companies and the fintech lenders that won't build it.
- **Payments phase:** stay a software router (funds settle directly into the merchant's own PSP accounts) until you decide to apply for a CBE licence or partner with a licensed PSP or bank.
- **Market size:** the number of lenders is small, so price per connected company and per report, and expand to B (distributors as SaaS customers) to grow revenue.

## 9. Validation plan (four weeks)

1. **Interview 10 lenders**: 4 factoring companies, Valu SME, 2 bank SME desks, 2 fintech lenders, 1 B2B marketplace. Ask how they underwrite an SME today, which documents they collect, how long it takes, and what they would pay for verified ETA invoice data.
2. **Interview 10 finance managers** at distributors and manufacturers about reconciliation time, payment channels, and unapplied cash.
3. **Technical spike**: register a test ERP system on the ETA pre-production environment, pull invoices, and normalise them into the `/invoices` model.
4. **Legal opinion** on ETA credential use and on Law 151/2020.
5. **Go/no-go:** go if three or more lenders agree to a paid pilot, or sign a letter of intent, for verified invoice and receivables data.

## 10. Deep dive: reconciliation is the core problem in B2B collections

Added after review: manual reconciliation in Excel is the biggest pain in payment collection. The analysis supports that, with one refinement. **The problem is concentrated on the rails that carry no structured reference**: bank transfers, InstaPay, wallets, cash and cheques. Card and Fawry payments already carry an order or reference number. Their pain is smaller: settlements arrive net of fees and batched.

### 10.1 Why it happens in Egypt, channel by channel

| Channel | What arrives at the seller | Why it doesn't match the invoice |
|---|---|---|
| **InstaPay** | A credit on the bank statement: amount, sender name, sometimes a short note | Payers usually send from a **personal** account (the owner, an accountant, a relative), so the name doesn't match the customer. There is no documented invoice-reference field or request-to-pay for businesses. **Proof of payment is a WhatsApp screenshot**, which is also a well-known fraud vector because screenshots can be faked |
| **Bank transfer / ACH** | A statement line with a truncated free-text narration | One transfer pays several invoices, or part of one. Bank charges are deducted. Narrations are cut off or empty. Statements usually come as an Excel or PDF export from e-banking. Structured formats (MT940/camt.053) mostly reach large corporates only **(verify per bank)** |
| **Mobile wallets** (Vodafone Cash and others) | A transfer from a personal phone number | No link to the customer account or invoice |
| **Cash through sales reps and drivers** | A paper receipt book, then a lump deposit days later | No link between the deposit and the individual invoices. Leakage and delays |
| **Cheques, often post-dated** | A cheque register, then clearing days later, and sometimes a bounce | It is tracked separately from the invoice. Bounces reopen the receivable |
| **PSPs** (Paymob, Kashier, Fawry) | A reference per transaction, but **net, batched settlement** to the bank (T+1 or more) | The bank shows one settlement line for hundreds of transactions minus fees. It has to be "exploded" against the PSP's settlement report |
| **Courier COD** (Bosta and others) | A weekly net settlement minus shipping fees and returns | Many-to-one with deductions. Couriers themselves hire AR accountants to manage this |

The result is days to weeks of delay before the receivable is marked paid, unapplied cash ("we got 47,500 EGP from someone"), wrong customer statements and disputes, credit limits blocked for customers who already paid, and no reliable data to finance.

### 10.2 What a real solution looks like

It has to work in three layers. Matching alone is not enough:

1. **Prevent: give every invoice or customer a unique payment identity at issuance.**
   - A payment link or Fawry reference code per invoice (through Paymob, Kashier or Fawry), sent by WhatsApp or SMS with the invoice.
   - **Virtual accounts per customer**, with the bank (the way Nigeria and India solved bank-transfer reconciliation). **(verify:** which Egyptian banks offer virtual accounts or virtual IBANs to corporates, and their reporting format; my search found no public CIB or NBE product page**)**.
   - An InstaPay QR or payment link per customer, once (and if) it can carry a reference.
2. **Capture: ingest every source, in whatever format it comes.**
   - PSP APIs and settlement reports.
   - Bank statements as Excel, PDF or MT940, uploaded, emailed, or pulled through a bank API where a partnership exists.
   - **WhatsApp and photo payment proofs**, read with OCR and an LLM, then checked against the bank statement. This also catches fake screenshots.
   - A collections app for reps: an offline receipt, a photo of the cheque, the cash amount, and the GPS location.
   - Courier COD settlement files.
3. **Match, post and chase.**
   - Rules plus fuzzy matching: amount tolerance, partial and many-to-many payments, settlement explosion, and **Arabic name normalisation and transliteration** (محمد / Mohamed / Mohammed; "Est." / مؤسسة).
   - A **payer map learned from confirmations**: "account of Ahmed Saeed pays for Al-Nour Market".
   - An exception queue for humans.
   - Posting back to the ERP (Odoo, ERPNext, local accounting tools) and sending customer statements and reminders.

**Metrics to sell on:** auto-match rate, days sales outstanding (DSO), unapplied cash, hours per month spent on reconciliation, and time to close the month.

### 10.3 Who is already working on this

- **In Egypt:** [SETTLE](https://launchbaseafrica.com/2024/09/18/egypts-settle-raises-2m-to-automate-b2b-payments-and-collections) raised a $2m pre-seed in Sept 2024. It connects ERPs (Oracle, SAP) to bank accounts through Egypt's ACH, targeting construction, energy and contracting. It is the closest competitor, but it is aimed at large corporates using bank rails. **(verify** its 2026 status and whether it handles InstaPay, cash and PSP sources**)**. Banks' cash-management products and the PSPs' own dashboards each cover only their own rail.
- **Globally:**
  - **Nigeria:** dedicated virtual accounts from Paystack, Flutterwave and Monnify. This is the best analogue, because Nigerian businesses are also paid mostly by bank transfer.
  - **India:** Razorpay Smart Collect and Cashfree virtual accounts and UPI IDs.
  - **Enterprise cash application:** HighRadius, Billtrust and Versapay.
  - **Developer-first reconciliation:** Modern Treasury.
  - **SME finance:** Midday (open source), Upflow.

### 10.4 Open source for this product

| Project | Use | License | Last commit |
|---|---|---|---|
| [Blnk](https://github.com/blnkfinance/blnk) | Ledger plus a **reconciliation engine with matching rules** (`reconciliation.go`, `api/reconciliation_api.go`) | Apache-2.0 | 2026-10-05 |
| [Midday](https://github.com/midday-ai/midday) | Invoice/receipt-to-transaction matching UX and a worker (`apps/worker/src/processors/inbox/match-transactions-bidirectional.ts`, `docs/inbox-matching.md`) | **AGPL-3.0**: study it, don't copy it | 2026-06-13 |
| [Hyperswitch](https://github.com/juspay/hyperswitch) | Creating payment links per invoice across PSPs (phase 2) | Apache-2.0 | 2026-10-07 |
| [ERPNext](https://github.com/frappe/erpnext) / Odoo | Their bank-reconciliation tools show the data model ERPs expect to receive | GPL-3.0 / LGPL-3.0 | 2026-10-07 |

### 10.5 What changes in the recommendation

- **Wedge: a reconciliation and collections product for B2B sellers.** Target distributors, manufacturers and B2B e-commerce sellers with about EGP 20m+ in revenue and 200+ active customers. They pay by subscription plus a fee per matched transaction. The product doesn't hold funds, so it needs no CBE licence, and it doesn't depend on the ETA-credentials question.
- **Second product: the lender data API (opportunity A).** It is built from the same normalised invoice and payment data, with the seller's consent. For example, the product can tell a factoring company that "this invoice was paid on time 9 times out of 10 by this buyer".
- **Third product: unified payment links and payouts (opportunity C).** This is the prevention layer, built once you have the customers.

### 10.6 Fast validation: a concierge test

Ask 5–10 finance managers for **last month's invoice export and bank statement**, and send back a matched file within 48 hours, with a Python script and you in the loop. Measure the auto-match rate you reach and how long their team spent on the same month. If at least three ask to do it again next month, and agree to pay, build the product.

## Sources

- [Avalara: E-invoicing in Egypt](https://www.avalara.com/us/en/vatlive/country-guides/africa-and-middle-east/egypt-vat/egyptian-e-invoicing.html)
- [VATupdate: E-invoicing and e-reporting in Egypt (Feb 2026)](https://www.vatupdate.com/2026/02/23/briefing-document-podcast-e-invoicing-e-reporting-in-egypt/)
- [Orchida Tax: Egypt e-invoicing compliance 2026](https://orchidatax.com/countries-compliance/egypt-e-invoicing-compliance/)
- [Data Value: Egypt e-invoicing 2026 SME guide](https://datavalue.solutions/egypt-e-invoicing-eta-2026-sme-guide/)
- [Daily News Egypt: FRA launches digital factoring portal (Feb 2026)](https://www.dailynewsegypt.com/2026/02/08/egypts-fra-launches-digital-factoring-portal-to-curb-financing-risks/)
- [BCR: FRA and e-finance digital factoring system](https://bcrpub.com/news/fra-and-e-finance-launch-digital-system-to-enhance-factoring-activities-in-egypt/)
- [Daily News Egypt: Valu receives FRA approval for SME financing (May 2026)](https://www.dailynewsegypt.com/2026/05/10/valu-receives-fra-approval-to-launch-sme-financing-arm/)
- [Disrupt Africa: Paymob raises $35m pre-Series C (Sept 2026)](https://disruptafrica.com/2026/09/30/egyptian-fintech-startup-paymob-raises-35m-pre-series-c-funding-round/)
- [Wamda: Lucky $23m Series B (Apr 2026)](https://www.wamda.com/2026/04/egypt-lucky-secures-23-million-series-b-expand-north-africa)
- [Africanist: Egypt startups overview](https://note.com/africanist/n/n5bd9c1759c56?hl=en)
- [Baker McKenzie: Egypt new regulation on payment solutions](https://insightplus.bakermckenzie.com/bm/banking-finance_1/egypt-new-regulation-on-payment-solutions)
- [Open Banking Tracker: Egypt](https://www.openbankingtracker.com/regulation/egypt-open-banking)
- [Inclusive Money: Egypt open finance](https://inclusivemoney.com/countries/egypt-open-banking)
- [The Fintech Times: Egypt fintech ecosystem in 2026](https://thefintechtimes.com/overview-of-the-fintech-ecosystem-in-2026-in-egypt/)
- [Egyptian Streets: Inside Egypt's InstaPay economy (Jan 2026)](https://egyptianstreets.com/2026/01/04/inside-egypts-instapay-economy-how-instant-payments-are-changing-access-for-a-new-generation/)
- [Electronic Payments International: X-ERA and Paymob B2B payments](https://www.electronicpaymentsinternational.com/news/x-era-paymob-digital-payments/)
- [IBS Intelligence: Egyptian SMEs' adoption of digital payments](https://ibsintelligence.com/ibsi-news/major-adoption-of-digital-payments-signals-sme-transformation-in-egypt-study-showsmajority-of-egypts-smes-now-prefer-digital-over-cash-study-shows/)
- [Enterprise: Egyptian firms on digital trade readiness (Sept 2026)](https://enterpriseam.com/egypt/2026/09/09/local-firms-are-ahead-of-the-global-average-on-digital-trade-readiness-standard-chartered-finds/)
- [The Paypers: Lean Technologies expands offering](https://thepaypers.com/fintech/news/lean-technologies-expands-its-offering-as-it-plans-its-ipo)
- [Biometric Update: Egypt approves banking eKYC framework (Aug 2026)](https://www.biometricupdate.com/202608/egypt-approves-banking-sector-ekyc-legal-framework-in-financial-inclusion-push)
- [Launch Base Africa: Egypt's SETTLE raises $2m to automate B2B payments and collections (Sept 2024)](https://launchbaseafrica.com/2024/09/18/egypts-settle-raises-2m-to-automate-b2b-payments-and-collections)
- [Enterprise: B2B payment platform SETTLE raises $2m](https://enterpriseam.com/egypt/2024/09/18/b2b-payment-platform-settle-raises-usd-2-mn-in-pre-seed-round/)
- [Ahram Online: InstaPay allows transfers via QR code](https://english.ahram.org.eg/NewsContent/3/1239/524682/Business/Tech/Instapay-allows-instant-money-transfers-via-QR-cod.aspx)
- [InstaPay Egypt on the App Store (release notes)](https://apps.apple.com/us/app/instapay-egypt/id1592108795)
- [Nilex: Payment gateways in Egypt 2026](https://nilexdigitalsystems.com/articles/payment-gateways-egypt-paymob-fawry-instapay)
- [Emirates NBD: Virtual accounts (comparison point)](https://www.emiratesnbd.com/en/corporate-and-institutional-banking/transaction-banking/digital-solution/virtual-accounts)
