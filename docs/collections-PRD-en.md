# PRD: B2B invoice collections platform (working name "Tahseel")

| | |
|---|---|
| **Status** | Draft 1 |
| **Date** | Oct 8, 2026 |
| **Owner** | khaled |
| **Related docs** | `docs/collections-PRD-ar.md` (Arabic version of this PRD), `docs/problem-and-solution-ar.md` (the problem and solution in detail), `docs/simple-model-ar.md` (the model), `docs/egypt-b2b-fintech-analysis.md` (market analysis and sources) |

> **Conventions:**
> - **P0** = required in the first release (MVP), **P1** = right after the MVP, **P2** = later.
> - **(verify)** = an assumption or fact to confirm with a partner, customer or lawyer before building.
> - Names and numbers in examples are **illustrative**.

---

## 1. Overview

### 1.1 The problem
In Egyptian B2B trade, **the invoice is issued in one place and the money arrives from somewhere else, with nothing linking the two.** Customers pay by InstaPay from a personal account, in cash to a sales rep, by post-dated cheque, through Fawry, from a mobile wallet, or by bank transfer with no invoice number.

So the finance team matches every pound to an invoice by hand in Excel. The results:
- **Delays** in closing invoices.
- **Unidentified money** sitting in the bank with nobody sure who it came from.
- **Customers who have paid** but still have their credit limit blocked.
- **Leakage** in the cash that sales reps hold.
- **Management can't see** the real cash position.

The full walkthrough with examples is in `problem-and-solution-ar.md`.

### 1.2 The solution
**The payment starts from the invoice itself.**
1. The seller issues the invoice, and every invoice has a **payment link**.
2. The buyer opens **one page with all their open invoices** (and, over time, invoices from all their suppliers on the network) and pays with the method that suits them.
3. **Sales reps collect cash and cheques against the invoice itself** in their app.
4. As soon as the payment is confirmed, the invoice **closes automatically** for both seller and buyer, an entry goes to the ERP, and the customer gets a receipt.
5. Money that arrives outside the link goes to an **exceptions screen** with smart matching suggestions.

### 1.3 The promise to the customer
> "Issue your invoice, and its money arrives already knowing which invoice it pays. The invoice closes by itself, with no Excel."

### 1.4 What we are, and what we are not
| We are | We are not |
|---|---|
| Software (SaaS) for the invoice → collection → close cycle | A bank, a payment company or a lender |
| The connection between a business and a licensed payment company (sponsor) | **We never hold funds.** Money goes to the seller's own account at the payment company or bank |
| The system that records, tracks and closes invoices | A lender (financing is a later phase, with a licensed partner) |

---

## 2. Goals and non-goals

### 2.1 Goals (first release and pilot)
1. **G1:** Any invoice paid through the link or collected in the rep app **closes automatically within one minute** of payment confirmation.
2. **G2:** The buyer can see all their open invoices with the seller and pay all or part of them, **without an app and without complicated sign-up**.
3. **G3:** Rep custody (cash and cheques) is **linked to invoices** and tracked in real time.
4. **G4:** Money that arrives outside the link is matched from an exceptions screen in **under 30 seconds per case** on average.
5. **G5:** The seller's ERP receives collection entries with no manual data entry.

### 2.2 Non-goals (not in this release)
- Financing, BNPL and factoring.
- Credit scoring as a product for lenders. (Showing payment behaviour to the seller itself is P1.)
- Paying the seller's own suppliers (the AP side). P2.
- Issuing e-invoices and submitting them to the tax authority (ETA). **We read invoices from the ERP or Excel, and we don't replace the ERP.** We store the e-invoice UUID if present.
- Holding any funds, or running a wallet.
- Currencies other than the Egyptian pound.

---

## 3. Users (personas)

| # | User | Example | Wants | Uses |
|---|---|---|---|---|
| U1 | **Collections accountant** at the seller | Mona, at "Al-Amal Distribution" | Close invoices fast and cut manual matching | Web dashboard |
| U2 | **Finance manager / owner** | Mr. Hesham | See real cash, overdue amounts and rep performance | Web dashboard |
| U3 | **Sales rep** | Mahmoud | Collect quickly, sell again to customers who have paid, close out their cash custody without disputes | Mobile app |
| U4 | **Sales manager** | Mr. Karim | Track reps' collections and custody | Web dashboard |
| U5 | **Buyer (shop or business owner)** | Hajj Sayed | Know how much they owe, pay easily, have proof of payment | Payment page (WhatsApp link) |
| U6 | **Buyer's bookkeeper** | Ahmed (Hajj Sayed's son) | Pay on the shop's behalf from his phone | Payment page |
| U7 | **Our operations team** | Ops | Onboard businesses, support them, monitor failures | Internal admin panel |

---

## 4. Success metrics

### 4.1 North Star
**Share of invoice value closed automatically** (paid through the link, collected in the rep app, or paid with a Fawry code) out of total collections in the month.

### 4.2 Secondary metrics
| Metric | Definition | Pilot target (hypothesis to measure) |
|---|---|---|
| Time to close | From payment confirmation to invoice closed | Under one minute for automatic closes |
| Link usage | Invoices paid through the link ÷ invoices paid | Measure it, and aim to grow it week over week |
| Rep app usage | Rep collections recorded in the app ÷ all their collections | Close to 100% (mandated by the seller) |
| Open exceptions | Count and value of money not yet matched | Falls every week |
| Time to resolve an exception | Average time per exception | Under 30 seconds |
| Custody discrepancies | Collected − handed over, per rep | Visible the same day |
| DSO | Average days to collect | Measure before and after |
| Buyer activation | Buyers who opened the link at least once ÷ buyers it was sent to | Measure it |

### 4.3 Business metrics
- Active sellers, active buyers, and monthly invoice value passing through the platform.
- Retention: sellers still active after 3 months.
- Revenue: subscriptions, fees, and a share of payment fees.

---

## 5. Scope and phases

| Phase | Contents | Rough timing |
|---|---|---|
| **MVP (v1)** | Invoice import, links, buyer page, one payment partner (Connect mode), rep app (cash and cheques), automatic close, WhatsApp reminders, bank-statement exceptions screen, ERP export, basic dashboard | 10–12 weeks |
| **v1.1** | Direct Odoo and ERPNext integration, Fawry code, payment-partner settlement, learned matching rules (payer aliases), API and webhooks, buyer disputes | +6 weeks |
| **v2** | Buyer network (one page for all suppliers), Managed mode with a sponsor, virtual accounts (if available), payment behaviour (scoring for the seller), the AP side | +3 months |
| **Later** | Financing with a lending partner, data API for lenders | After collections is proven |

---

## 6. Concepts and data model

### 6.1 Core entities
| Entity | Description | Key fields |
|---|---|---|
| **Organization** | A business on the platform (a seller, and in v2 also a buyer) | Name, tax ID, commercial registration, settings, plan |
| **Branch** | A branch or sales area | Name, manager |
| **User** | A user at the seller | Name, mobile, email, role, branches |
| **Customer** | The seller's customer (the buyer, from the seller's point of view) | Customer code at the seller, name, mobiles, tax ID (optional), rep, credit limit, branch |
| **BuyerIdentity** | The buyer's identity across the network (v2) | Verified mobile, tax ID. Links the same buyer across several sellers |
| **Invoice** | The invoice | Invoice number, customer, date, due date, total, paid, remaining, status, ETA UUID (optional), source (Excel/Odoo/API), PDF attachment |
| **CreditNote** | A credit note or return | Number, customer, amount, related invoice (optional) |
| **PaymentLink** | A payment link | Short code, scope (one invoice, or all of a customer's invoices), expiry, status |
| **PaymentIntent** | A payment attempt from the page | Amount, selected invoices, method, order ID at the payment company, status |
| **Payment** | A confirmed payment | Amount, method, source (link / rep / Fawry / bank statement / manual), external reference, payer, date |
| **Allocation** | Links a payment to an invoice | Payment, invoice, amount. **One payment can be split across several invoices** |
| **CustomerCredit** | A customer's credit balance | From an overpayment or a credit note |
| **Receipt** | A receipt for the buyer | Number, payment, PDF or link |
| **RepCollection** | A rep's collection | Rep, customer, invoices, amount, type (cash/cheque), GPS, time, sync status |
| **Cheque** | A cheque | Bank, number, date, amount, image, status, customer, invoices |
| **Custody** | The cash and cheques a rep holds | Rep, current balance (cash and cheques), movements |
| **Deposit** | A rep handing over custody | Rep, amount, method (bank/Fawry/cashier), reference, collections it covers |
| **BankStatement / StatementLine** | A bank statement and its lines | Bank, account, date, amount, description, payer name, status |
| **Settlement** | A settlement from the payment company | Date, gross, fee, net, transactions |
| **Exception** | Something that needs a human | Type, amount, source, suggestions, status, assignee |
| **PayerAlias** | "This payer pays on behalf of this customer" | Name, account or mobile, customer, confidence, how it was learned |
| **Reminder** | A scheduled or sent reminder | Invoice or customer, template, channel, status |
| **Dispute** | A buyer's dispute (v1.1) | Invoice, reason, status |
| **Integration** | An external connection | Type (Paymob/Fawry/Odoo/...), encrypted settings, status |
| **AuditLog** | A record of every change | Who, when, what, before and after |

### 6.2 Invoice states (state machine)
```
draft ──► open ──► partially_paid ──► paid
            │            │
            ├──► overdue ◄┘  (automatic after the due date if a balance remains)
            ├──► disputed (v1.1) ──► open
            ├──► pending_cheque (a cheque being collected covers it)
            └──► cancelled / written_off (with permission)
```
- `paid` means **remaining = 0** after all allocations and credit notes.
- An invoice covered by a cheque that hasn't cleared is `pending_cheque`. If the cheque clears it becomes `paid`; if it bounces it goes back to `open` or `overdue`.

### 6.3 Payment states
```
PaymentIntent: created ──► pending ──► succeeded ──► (Payment)
                               └──► failed / expired
Payment: confirmed ──► allocated (fully/partly) ──► settled (confirmed in the settlement or bank)
                └──► reversed (refund/chargeback/cancellation)
```

### 6.4 Cheque states
`received` → `in_custody` (with the rep) → `handed_over` (to the cashier or bank) → `deposited` → `cleared` | `bounced`

### 6.5 General financial rules
- **All amounts are stored as integers in piasters.** No floats.
- Currency is EGP only in v1.
- **Time:** Africa/Cairo for every displayed date; UTC in storage.
- **Default allocation:** to the invoices the buyer selected. If the buyer chose "pay an amount", the oldest due date goes first (FIFO). The seller can change the rule.
- **No allocation may leave a negative balance.** Any excess goes to `CustomerCredit`.
- **Every financial operation is idempotent** on an external key (the payment company's transaction ID, or a collection ID generated on the rep's device).

---

## 7. User journeys (end to end)

### J1: Seller onboarding (P0)
1. Ops creates the Organization and invites the admin.
2. The admin enters the basics: name, tax ID, logo and branches.
3. They connect the payment company by entering their own Paymob or Kashier account credentials (Connect mode). The system runs a test transaction.
4. They import customers (Excel) and assign them to reps.
5. They import open invoices (Excel), or connect the ERP (v1.1).
6. They set the rules: payment methods, who pays the fee, the minimum partial payment, the reminder schedule and the message templates.
7. They invite reps by SMS with a link to download the app.
8. **"Send the first batch of links":** a welcome message to customers with their statement.

**Acceptance:** a new seller can send its first links to customers within one working day, with help from Ops.

### J2: New invoice → link (P0)
1. The invoice arrives (daily Excel, API or ERP).
2. The system creates a `PaymentLink` for the customer (a fixed statement link per customer) and one for the invoice.
3. A WhatsApp message goes to the customer using the approved template: seller name, invoice number, amount, due date and link.
4. If WhatsApp fails, an SMS is sent.

### J3: The buyer pays (P0)
1. Ahmed opens the link → **OTP to the customer's registered mobile** (only the first time on a given device; the session length is configurable).
2. He sees the total due and the invoices (number, date, due date, remaining, overdue ones highlighted), and can open any invoice's PDF.
3. He chooses **everything due**, **specific invoices**, or **an amount** (≥ the minimum).
4. He picks a method, from those available for the seller's rules and the amount: card, wallet, InstaPay (if available), Fawry code (v1.1), or bank transfer with a reference number.
5. **Card and wallet:** he is sent to the payment company's hosted page. We never touch card data.
6. The payment company returns the result, and its webhook confirms it.
7. **At the same moment:** Payment → Allocation → invoices close → Receipt → WhatsApp message to the buyer → notification to the rep and the seller → an entry queued for the ERP export.
8. **Bank transfer with a reference:** the page shows the account details and the reference (for example `N7Q2K`), and the status shows "waiting for transfer". Confirmation comes from the bank statement (J7).

### J4: The rep collects cash (P0)
1. Mahmoud opens the customer in the app and sees open invoices and the credit limit.
2. He chooses "Collect cash" and selects invoices or enters an amount.
3. He confirms. **The collection is recorded on the device even with no internet**, with a unique ID generated on the device.
4. **Receipt:** with internet, a WhatsApp message goes to the buyer immediately. Without it, the receipt is sent as soon as the app syncs, and he can also show a receipt QR code on screen.
5. The invoices close at the seller **as "collected by rep"**, and the cash is added to the rep's custody.
6. **Protection:** the buyer receives a message with the amount and the rep's name, so any manipulation shows up.

### J5: The rep collects a cheque (P0)
1. "Collect cheque": bank, number, due date, amount, a photo of the cheque (required), and the invoices.
2. The invoices become `pending_cheque` and the cheque enters the rep's custody.
3. The cheque is handed to the cashier ("receive cheques" on the web, by scanning or selecting).
4. Deposit and clearing are updated manually on the web (v1), or from the bank statement (v1.1).
5. **Bounced cheque:** the invoices reopen, the rep and manager are alerted, and the customer is flagged.

### J6: Handing over custody (P0)
1. At the end of the day the app shows: "Your custody: 85,000 cash + 2 cheques".
2. Mahmoud hands it over:
   - **At the bank:** he enters the deposit reference, the amount and a photo of the deposit slip.
   - **At Fawry:** with his own deposit code (v1.1).
   - **To the cashier:** the cashier confirms receipt on the web.
3. The system links the deposit to the collections. **Any difference is shown** to the manager immediately.
4. When the bank line or the settlement arrives, the deposit is confirmed.

### J7: Bank statement and exceptions (P0)
1. Mona uploads the bank statement (Excel or CSV). In v1.1, also PDF and a dedicated email address.
2. The system reads the lines, removes those already confirmed (duplicates) and matches:
   - Payment-company settlement lines → Settlement.
   - Lines with a reference (`N7Q2K` or an invoice number) → automatic payment.
   - Rep deposits → Deposit.
   - The rest → **Exceptions**, with suggestions.
3. Mona opens an exception and sees the line and the top 3 suggestions (customer, invoices, confidence and reason). Then she can:
   - **Confirm** → payment + allocation + receipt + **learn a PayerAlias**.
   - **Pick a customer and invoices manually.**
   - **"Ask the customer"** (v1.1): a message to the likely buyer.
   - **"Not a collection"**: other income, or an internal transfer.

### J8: Reminders (P0)
- Default schedule: 3 days before the due date, on the due date, then 3, 7 and 14 days after, then escalation to the rep (a task in the app).
- **Reminders stop** when the invoice is paid, disputed, or covered by a cheque being collected.
- **Sending hours:** 10am–8pm, not on Fridays (configurable).
- At most one message per customer per day: **reminders are combined into one message** covering all invoices.

### J9: ERP export and month-end close (P0)
- **v1:** a daily or on-demand export file (Excel or CSV) of collection entries: date, customer, invoice, amount, method and reference. The format is configurable per ERP; we start with Odoo, ERPNext and a generic template.
- **v1.1:** direct posting to Odoo and ERPNext through their APIs.
- **Month-end report:** collections by method, matched items, open exceptions, open custody, and cheques being collected.

---

## 8. Functional requirements

### M1. Account and settings
| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-101 | Create an Organization with basic details and logo | P0 | The seller's name and logo appear in messages and on the page |
| FR-102 | Branches and sales areas, with users and customers assigned to them | P1 | A rep sees only their branch's customers |
| FR-103 | Payment-method settings per amount range (for example card only up to 10,000) | P0 | The page shows methods according to the selected amount |
| FR-104 | Who pays the fee: the seller, or the buyer as a service fee **(verify the payment companies' rules)** | P1 | If enabled, the fee is shown to the buyer before paying |
| FR-105 | Minimum partial payment (amount or percentage) | P0 | The page rejects anything below the minimum |
| FR-106 | Reminder schedule and message templates | P0 | Changes apply to new invoices |
| FR-107 | Bank account details for transfers, and the reference format | P0 | Shown on the payment page |

### M2. Customers
| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-201 | Import customers from Excel (template in Appendix A) with an error report | P0 | Bad rows are shown with the reason; the rest are saved |
| FR-202 | Several mobiles per customer, choosing which may pay and receive messages | P0 | OTPs go only to allowed mobiles |
| FR-203 | Assign each customer a rep and a credit limit | P0 | The rep sees the limit and the available amount in real time |
| FR-204 | Customer page: invoices, payments, cheques, messages, aliases and history | P0 | All activity in time order |
| FR-205 | Merge duplicate customers | P1 | Invoices and payments move over with nothing lost |
| FR-206 | Payment behaviour summary per customer (average days late, bounced cheques, share paid through the link) | P1 | Shown on the customer page and in the app |

### M3. Invoices and import
| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-301 | Import invoices from Excel or CSV (template in Appendix B): new, updated and cancelled | P0 | Re-uploading the same file creates no duplicates (key = the seller's invoice number) |
| FR-302 | Create an invoice manually on the web (for sellers with no ERP) | P1 | The invoice is produced as a PDF with the seller's logo |
| FR-303 | Store the ETA UUID and show it on the page if present | P0 | Shown under the invoice number |
| FR-304 | Credit notes and returns: import them and apply to an invoice or as a credit | P0 | The remaining balance is correct |
| FR-305 | Odoo integration (API): pull invoices and credit notes every X minutes | P1 | A new invoice appears in under 15 minutes |
| FR-306 | ERPNext integration (API) | P1 | Same as FR-305 |
| FR-307 | Public API to create and update invoices | P1 | Documented with OpenAPI |
| FR-308 | Attach the invoice PDF (from the ERP, or generated by us) | P0 | The buyer can open it from the page |
| FR-309 | Editing or cancelling an invoice that has payments is **forbidden**, except by undoing the allocation with permission | P0 | Recorded in the audit log |

### M4. Links and the buyer page
| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-401 | A fixed link per customer (statement) and a link per invoice | P0 | The short link reveals no data without an OTP |
| FR-402 | OTP verification to an allowed mobile, with a session on the same device for a configurable period (default 30 days) | P0 | 5 wrong attempts = locked for 15 minutes |
| FR-403 | Show what is due: total, invoices, overdue, and credit balance | P0 | Figures match the seller's in real time |
| FR-404 | Choose: everything, specific invoices, or an amount | P0 | The allocation is shown before paying ("this will close: 412, 418") |
| FR-405 | Show the available methods according to the rules and the amount | P0 | — |
| FR-406 | Pay by card or wallet through the payment company's hosted page, then return to our page with the result | P0 | **We hold no card data** |
| FR-407 | Bank transfer or InstaPay with a reference: show the details, copy in one tap, and a "waiting" status | P0 | The reference is unique per customer (or per intent) |
| FR-408 | InstaPay through the payment company, with a notification **(verify that the partner supports it)** | P1 | — |
| FR-409 | Fawry code for paying cash at outlets | P1 | The code is generated for the selected amount and its expiry is shown |
| FR-410 | Payment and receipt history for the buyer | P0 | A PDF download for each receipt |
| FR-411 | Arabic (RTL) first, English optional. Mobile-first and light on weak connections | P0 | The page loads in under 3 seconds on 3G |
| FR-412 | "I have a dispute" button on an invoice (stops reminders and alerts the seller) | P1 | — |
| FR-413 | **Buyer network:** if the same buyer (BuyerIdentity) has invoices with several sellers on the platform, they see them all on one page, **with their consent** | P2 (v2) | No seller ever sees another seller's data |

### M5. Payments and the payment partner
| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-501 | Connect at least one payment company in Connect mode (the seller's own account). Start with one partner (Paymob or Kashier) **(verify the partner)** | P0 | A successful test transaction during onboarding |
| FR-502 | Receive webhooks, verify the signature, and enforce **idempotency** on the transaction ID | P0 | The same webhook twice = one payment |
| FR-503 | **Fallback polling** for intents still pending after X minutes | P0 | No successful payment stays unconfirmed for more than 15 minutes |
| FR-504 | Refunds from the web with permission, reversing the allocation | P1 | The invoice reopens for the amount |
| FR-505 | Record a manual payment (for methods outside the platform), with permission and a reason | P0 | Recorded in the audit log |
| FR-506 | Payment-company settlement: pull the settlement report and match it to the transactions and to the bank line | P1 | Any difference becomes an exception |
| FR-507 | Fawry as a biller or collector (online: inquiry and notification) **(verify with Fawry)** | P1 | — |
| FR-508 | Managed mode (sub-merchant under a sponsor) | P2 | — |

### M6. Allocation
| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-601 | Allocate a payment to the selected invoices, or FIFO | P0 | Sum of allocations = payment amount − any resulting credit |
| FR-602 | Excess → credit balance, applied to future invoices (automatically or manually, per setting) | P0 | — |
| FR-603 | Undo and redo an allocation, with permission | P0 | Full audit trail |
| FR-604 | Tolerance for small differences (for example ≤ EGP 50 or ≤ 0.5%), closed as a bank charge or discount per setting | P1 | The difference is recorded on a separate line |

### M7. Rep app (Android first)
| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-701 | Sign in with mobile and OTP, bound to one device (changing device needs manager approval) | P0 | — |
| FR-702 | Customer list (search, overdue first), each customer's invoices, and available credit | P0 | Data stored offline; last sync time shown |
| FR-703 | Collect cash against invoices or an amount, **offline** | P0 | Unique ID on the device; no duplicates after sync |
| FR-704 | Collect a cheque, with a required photo | P0 | — |
| FR-705 | WhatsApp receipt to the buyer, and a receipt QR on screen | P0 | The receipt arrives within one minute of sync |
| FR-706 | "Send the link to the customer" (WhatsApp) | P0 | — |
| FR-707 | Custody screen: cash, cheques and movements | P0 | — |
| FR-708 | Record a handover (bank, Fawry or cashier) with a reference and a photo of the slip | P0 | The difference between custody and handover is shown |
| FR-709 | Record GPS and time for each collection | P0 | (Location permission required) |
| FR-710 | Collection tasks (from escalation) and visit tasks | P1 | — |
| FR-711 | Maximum cash custody (manager alerted if exceeded) | P1 | — |
| FR-712 | iOS | P2 | — |

### M8. Cheques
| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-801 | Cheque register with states (6.4) and filters by due date | P0 | — |
| FR-802 | Cashier receives cheques from reps (scan or select) | P0 | — |
| FR-803 | Update status: deposited, cleared or bounced (one by one or in bulk) | P0 | A bounce reopens the invoices and sends alerts |
| FR-804 | Alerts for cheques nearing their due date | P1 | — |
| FR-805 | Match cheque clearing from the bank statement | P1 | — |

### M9. Messages and reminders
| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-901 | WhatsApp Business API with Meta-approved templates, and SMS as fallback | P0 | Status of every message (sent, delivered, read, failed) |
| FR-902 | Templates: new invoice, reminder, overdue, receipt, welcome and OTP | P0 | Appendix C |
| FR-903 | Combine reminders: one message per customer per day | P0 | — |
| FR-904 | Sending hours and excluded days | P0 | — |
| FR-905 | Stop reminders for invoices that are paid, disputed, or covered by a cheque | P0 | — |
| FR-906 | Buyer can opt out of reminders (receipts still sent) | P1 | — |

### M10. Bank statements and exceptions
| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-1001 | Upload an Excel or CSV statement, with a column mapping saved per bank | P0 | The second upload from the same bank needs no mapping |
| FR-1002 | Prevent duplicates (the same line in two statements) | P0 | Hash of date, amount, description and balance |
| FR-1003 | Automatic rules: reference number, invoice number (regex per the seller's format), payment-company settlement, rep deposit | P0 | — |
| FR-1004 | Suggestions for the rest: PayerAlias, amount = an open invoice or a set of open invoices, fuzzy name (Arabic/Latin normalisation), date window | P0 | Top 3 suggestions with confidence and a written reason |
| FR-1005 | Exceptions screen: sorted by amount and age, one-click confirm, manual selection, "not a collection" | P0 | Resolved in under 30 seconds on average |
| FR-1006 | Learning: every confirmation creates or strengthens a PayerAlias | P0 | The same payer with the same pattern matches automatically next time (if confidence ≥ the threshold) |
| FR-1007 | Configurable confidence threshold for automatic matching (default: automatic only for explicit references at first) | P0 | — |
| FR-1008 | Read PDF statements | P1 | — |
| FR-1009 | Dedicated email address for receiving statements | P1 | — |
| FR-1010 | "Ask the customer" (a message to the likely buyer) | P1 | — |

### M11. Export and ERP integration
| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-1101 | Export collection entries (Excel or CSV) with Odoo, ERPNext and generic templates | P0 | Each entry is exported only once and marked "exported" |
| FR-1102 | Direct posting to Odoo and ERPNext | P1 | The payment appears in the ERP linked to the invoice |
| FR-1103 | Webhooks for the seller (invoice.paid, payment.created, ...) | P1 | HMAC signature, with retries |

### M12. Dashboard and reports
| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-1201 | Home: collected today, this week and this month, by method. Due and overdue, open exceptions, open custody | P0 | Figures in real time |
| FR-1202 | Receivables ageing (0–30, 31–60, 61–90, 90+) by customer, rep and branch | P0 | Excel export |
| FR-1203 | Rep report: collected, handed over, differences, late handovers | P0 | — |
| FR-1204 | Cheque report | P0 | — |
| FR-1205 | North Star: share closed automatically, and link usage | P0 | — |
| FR-1206 | DSO and its trend | P1 | — |
| FR-1207 | Month-end close report | P0 | — |

### M13. Users, permissions and audit
| Role | Permissions |
|---|---|
| **Owner/Admin** | Everything: settings, integrations, users |
| **Finance** | Invoices, payments, exceptions, cheques, export. No sensitive settings |
| **Sales Manager** | Customers, reps, custody, reports. Cannot edit payments |
| **Rep** | The app only, and only their own customers |
| **Viewer** | Read only |

| ID | Requirement | Priority |
|---|---|---|
| FR-1301 | The roles above, with branch assignment | P0 |
| FR-1302 | Audit log for every financial operation and every settings change (who, when, before and after, IP or device) | P0 |
| FR-1303 | 2FA for Admin and Finance | P1 |
| FR-1304 | Second-person approval (maker-checker) for refunds, write-offs and allocation reversals above a set amount | P1 |

### M14. Internal admin panel (Ops)
| ID | Requirement | Priority |
|---|---|---|
| FR-1401 | Create sellers, manage plans, enable and disable accounts | P0 |
| FR-1402 | Monitor integration health (failed webhooks, late syncs, failed messages) | P0 |
| FR-1403 | Log in as a seller for support (impersonation), with logging and approval | P1 |

---

## 9. Non-functional requirements

| Area | Requirement |
|---|---|
| **Security** | TLS everywhere. Secrets (payment-company credentials) encrypted with KMS. **No card data on our side** (the payment company's hosted page), so a smaller PCI scope. Rate limiting on OTPs and links. Short links are random and unguessable |
| **Privacy** | Compliance with Personal Data Protection Law 151/2020 **(verify registration and licensing requirements)**. Data minimisation, a retention policy, buyer consent for the supplier network (v2), and **complete separation of each seller's data (multi-tenant isolation)** |
| **Financial integrity** | Idempotency on every operation. Integer amounts. An internal double-entry ledger for payments, allocations and custody. No financial record is ever deleted, only reversed |
| **Availability** | 99.5% in the MVP. Webhooks are stored and processed from a queue, so nothing is lost if a component goes down |
| **Performance** | Buyer page under 3 seconds on 3G. Close within one minute of the webhook. A 5,000-line statement processed in under two minutes |
| **Offline** | The rep app works a full day without internet and syncs safely with no duplicates |
| **Language** | Arabic RTL first, Western digits for amounts (configurable), dates in Cairo time |
| **Monitoring** | Logs, metrics and alerts: webhook failures, late syncs, failed messages, settlement differences |
| **Backups** | Daily, with point-in-time recovery (PITR) |
| **Data residency** | **(verify)** whether financial data must be hosted inside Egypt |

### Architecture notes (proposed)
- **Backend:** Postgres. An internal ledger (could take inspiration from Blnk, Apache 2.0). A queue for webhooks, messages and sync.
- **Integrations layer:** one connector per payment company and per ERP, behind a common interface. Hyperswitch (Apache 2.0) could unify payment companies in v2.
- **Buyer page:** a lightweight server-rendered (SSR) web page without a heavy framework.
- **Rep app:** Android, with a local database (SQLite) and a sync queue.
- **Matching:** a rules engine plus scoring. LLM or OCR for PDFs and screenshots only from v1.1, and only under a confidence threshold.

---

## 10. Integrations

| Integration | Purpose | Phase | Status |
|---|---|---|---|
| Payment partner (Paymob or Kashier) | Card and wallet, webhooks, settlements | MVP | **Verify: the partner, pricing and InstaPay** |
| WhatsApp Business API (through a BSP) | Messages, templates and OTP | MVP | Templates need Meta approval |
| SMS provider | WhatsApp fallback and OTP | MVP | — |
| Excel or CSV | Invoices, customers and statements | MVP | — |
| Odoo / ERPNext | Pull invoices and post payments | v1.1 | — |
| Fawry | Payment code, biller inquiry and notification | v1.1 | **Verify with Fawry** |
| Banks | Statements (files), then API or virtual accounts | MVP (files), v2 (API) | **Verify** |
| E-invoicing system (ETA) | UUID verification | v2 | **Legal verification needed** |

---

## 11. Pricing (hypotheses for the pilot)

- **Pilot:** free for 8 weeks in exchange for data and feedback.
- **Afterwards (hypothesis to test in interviews):**
  - A monthly subscription by number of invoices or active customers, including a number of reps.
  - Or a small fee per invoice closed automatically.
  - In Managed mode (v2): a share of the payment company's fee.
- **Fees:** large invoices are steered to bank transfer or InstaPay with a reference, because it's cheaper. Card is for small amounts. **(Verify the partners' pricing.)**

---

## 12. Release plan

| Weeks | Deliverables |
|---|---|
| 0–2 | Confirm the payment partner, get WhatsApp templates approved, data model, ledger, Excel import |
| 3–5 | Buyer page, links, OTP, card and wallet payment, webhooks, automatic close, receipts |
| 6–8 | Rep app (cash, cheques, offline), custody and handover, cheques |
| 9–10 | Bank statement upload, exceptions and suggestions, ERP export, reminders, dashboard |
| 11–12 | Acceptance testing with the first distributor (UAT), training, limited trial run |
| 13–20 | **Pilot:** one distributor, 50–100 buyers, 3 reps, weekly measurement |

### Pilot plan
- **Before:** measure a full month the old way: matching time, unidentified amounts, DSO and custody discrepancies.
- **During:** a weekly report on the metrics (section 4), and interviews with 10 buyers, 3 reps and Mona.
- **Go/no-go:**
  - The distributor wants to continue and to pay.
  - The automatic-close share rises week over week.
  - Reps use the app for every collection.

---

## 13. Risks, open questions and out of scope

### 13.1 Risks
| Risk | Impact | Mitigation |
|---|---|---|
| **Buyers don't use the link** | Automatic closes stay low | The rep app still closes cash collections. Incentives: the credit limit is restored immediately, an instant receipt, and tying rep commission to digital collection (the seller's decision) |
| **Payment companies' fees are high on large invoices** | The seller refuses | Method rules by amount, transfers with references, and negotiating B2B pricing |
| **Reps resist** (they lose the flexibility of cash) | Leakage continues | A management decision by the seller, a fast and simple app, and buyer receipts that expose manipulation |
| **Bank statement formats change** | Import fails | A saved mapping per bank, and fast support from Ops |
| **Dependence on one payment partner** | Outage | A common connector layer from day one |
| **WhatsApp templates rejected or restricted** | Messages don't arrive | SMS fallback, and following Meta's policies |
| **Regulation** (whether any part needs a licence) | Shutdown | We never hold funds, and we get a legal opinion before launch |

### 13.2 Open questions
1. Which payment company will be the first partner, and what is its B2B pricing?
2. Is InstaPay available through the partner, with a real-time notification and a reference?
3. Do the payment company's rules allow the seller to charge the buyer a service fee?
4. Do any banks offer virtual accounts to businesses, and in what reporting format?
5. What are Fawry's terms for a biller integration run by a technical provider?
6. What do Law 151/2020 and data residency require?
7. Which sector first: FMCG, pharma or building materials? (To be decided from interviews.)
8. The final product name.

### 13.3 Out of scope (this release)
Financing, BNPL and factoring; scoring for lenders; supplier payments (AP); e-invoice issuing; wallets; foreign currencies; iOS; and the multi-seller buyer network (v2).

---

## Appendices

### Appendix A: Customer import template
| Column | Required | Example |
|---|---|---|
| customer_code | ✅ | C-1042 |
| name | ✅ | Al-Nour Supermarket |
| mobile_1 | ✅ | 01xxxxxxxxx |
| mobile_2 | — | 01xxxxxxxxx |
| tax_id | — | 123-456-789 |
| rep_code | ✅ | R-07 |
| credit_limit | — | 100000 |
| branch | — | Faisal |
| address | — | — |

### Appendix B: Invoice import template
| Column | Required | Example |
|---|---|---|
| invoice_number | ✅ | 2026-00412 |
| customer_code | ✅ | C-1042 |
| issue_date | ✅ | 2026-10-07 |
| due_date | ✅ | 2026-11-06 |
| total_amount | ✅ | 12500.00 |
| paid_amount | — | 0 |
| status | — | open / cancelled |
| eta_uuid | — | 3F7A… |
| type | — | invoice / credit_note |
| related_invoice | — | (for a credit note) |
| pdf_url | — | — |

### Appendix C: Message templates (draft; sent in Arabic, English shown for reference)
**New invoice:**
> {Seller name}: invoice {number} for EGP {amount}, due {date}. View and pay: {link}

**Reminder before due date (combined):**
> {Seller name}: you have {count} invoices totalling EGP {amount}, the earliest due {date}. Pay or view your statement: {link}

**Overdue:**
> {Seller name}: overdue invoices totalling EGP {amount}. Please pay: {link}. If you dispute an invoice, open the link and choose "I have a dispute".

**Receipt:**
> {Seller name}: we received EGP {amount} ({method}{, collected by rep X}). Paid: {invoice numbers}. Your remaining balance: EGP {remaining}. Receipt: {link}

**OTP:**
> Your verification code: {code}. Don't share it with anyone.

### Appendix D: Example webhook payload from the platform to the seller (v1.1)
```json
{
  "event": "invoice.paid",
  "occurred_at": "2026-11-05T10:42:13+02:00",
  "data": {
    "invoice_number": "2026-00412",
    "customer_code": "C-1042",
    "amount_paid_piasters": 1250000,
    "remaining_piasters": 0,
    "payment": {
      "id": "pay_8f3k2",
      "method": "card",
      "source": "payment_link",
      "external_ref": "PSP-998877"
    }
  }
}
```

### Appendix E: Matching rules on the exceptions screen (order applied)
1. **Explicit reference** in the description (the link's reference, the customer code, or an invoice number in the seller's format) → automatic.
2. **Payment-company settlement** (description and amount = a settlement report) → Settlement.
3. **Rep deposit** (reference, or amount and date = a recorded Deposit) → confirms the handover.
4. **PayerAlias + amount** = one open invoice or a set of open invoices for that customer → high-confidence suggestion (automatic if the setting allows).
5. **Fuzzy name** (after Arabic/Latin normalisation) + amount within tolerance + date window → suggestion.
6. **No match** → exception with no suggestion, resolved manually.
