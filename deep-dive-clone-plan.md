# Deep Dive: Building a Clone That Can Be Sold, Step by Step

Researched October 2026. Prices and figures come from the sources listed at the end. Recheck them before you commit money.

---

## 0. The License Question, Settled

The plan of copying code under a restrictive license, changing the UI and assuming nobody will find out is not one I'll help with. It is copyright infringement. It also tends to fail in practice:

- **Code scanners find it.** Acquirers, larger customers and investors run software-composition scans (FOSSA, Black Duck and similar). These tools match code fingerprints, not UI. Copied open-source code survives a redesign. The 2026 OSSRA report found license conflicts in 68% of audited codebases. In one documented case, AGPL violations found in due diligence cut $10M from a valuation and nearly killed the deal.
- **The code leaks clues.** Database schemas, API routes, error strings, websocket event names and JS bundle contents are all visible in a browser's network tab.
- **People leave.** Contractors and ex-employees know what the product was built on.
- **Owners enforce.** n8n and others actively enforce their licenses, and GPL enforcement cases keep moving in the courts.

**You don't need to hide anything.** What you actually want is to take existing code, change it heavily, keep your changes closed, and put your own brand on it. The **MIT and Apache licenses allow exactly that**, legally and openly. The only real obligation is to keep the original copyright and license notice in your source and in a "third-party licenses" page, which no customer will ever look at. So the rule for this plan is simple:

> **Build only on MIT/Apache-licensed code**, and stay out of any `/ee` or `/enterprise` folders. For anything under AGPL or source-available licenses, use the product only as a reference for features and behavior and write your own code. That is called a clean-room rewrite, and copying ideas, features and workflows that way is legal.

This also changes my earlier recommendation. **FreeScout is AGPL, and hosted FreeScout is already a commodity** (PikaPods sells it from about $3.10/month). So it is out. The base below is **Chatwoot**, whose core is MIT-licensed.

---

## 1. Opportunity Map (Permissive-License Bases Only)

| Incumbent (proof of revenue) | What customers hate (2026) | MIT/Apache base | Already crowded with cheap clones? | Verdict |
|---|---|---|---|---|
| **Gorgias, Zendesk, Help Scout, Intercom** (help desk + AI support) | Per-seat pricing **plus per-resolution AI fees**: Zendesk $55–115/agent + $1.50–2.00 per AI resolution; Help Scout $25–75/user + $0.75 per AI answer; Intercom Fin $0.99/resolution; Gorgias $0.90–1.00/AI resolution, with each AI resolution *also* billed as a ticket | **Chatwoot** core (MIT, natively multi-tenant, has email, chat, WhatsApp, FB and IG channels) | Partly. Many AI-support startups exist, but few offer a *full help desk* with flat AI pricing | **#1, deep dive below** |
| **Zapier** (~$310M revenue in 2024, ~100K paying customers) and **n8n** ($100M ARR in April 2026) | Zapier per-task pricing climbs steeply. n8n's license **forbids white-label and resale**, which frustrates agencies | **Activepieces** core (MIT) | The white-label niche is thin | **#2: white-label automation platform for agencies** |
| Atlassian Statuspage ($29–399+/mo) | Price per page and per subscriber | Uptime Kuma (MIT) | **Yes**: Instatus $15, OpenStatus $30, Better Stack free tier | Skip |
| Canny ($79/mo → $656/mo as tracked users grow) | Tracked-user pricing | Build from scratch (simple) | **Yes**: Nolt $29 flat, fdback $15, Produktly €19, Featurebase | Skip: the wedge is already taken |
| Mailchimp (free plan cut to 250 contacts, repeated increases) | Contact-based pricing | listmonk is AGPL, so clean-room only | Yes: hosted listmonk (xCloud, Opsily), MailerLite, Sendy | Skip for now: crowded, and spam abuse can get your sending blocked |
| Google Analytics / Plausible ($3.1M ARR with ~5 people) | Privacy, complexity | Umami (MIT) | **Yes**: Plausible, Fathom, Umami Cloud, GA is free | Skip. Proof the model works, but the niche is taken |

**The pattern:** The best opening is where the incumbents' **AI pricing is per outcome** and the market is still repricing. That is customer support in 2026. LLM costs keep falling, yet incumbents charge $0.75–2.00 per AI answer. That gap is your margin.

---

## 2. #1 Deep Dive: Flat-Priced AI Help Desk for Shopify Stores

**One-line pitch:** *"Your whole support team plus an AI agent that refunds, tracks and edits orders, for one flat monthly price. No per-ticket or per-resolution fees."*

### 2.1 Why Shopify stores first (not "all businesses")
1. **Built-in distribution.** The Shopify App Store has buyers searching "helpdesk" with purchase intent, and Shopify handles billing (Shopify Billing API). B2B help desks have no equivalent marketplace.
2. **The pain is sharpest here.** E-commerce support is high-volume and repetitive ("Where is my order?", returns, address changes). Per-ticket and per-resolution pricing hurts most at high volume.
3. **The AI can actually resolve tickets.** Order lookup, cancellation, address edits and return starts are API actions. Gorgias says its AI caps out at about 60% automation, so the potential is proven.
4. **The incumbent has a known weakness.** Gorgias bills each AI resolution twice: once as an AI resolution ($0.90–1.00) and once as a ticket.

Later, the same codebase expands to SaaS and B2B teams (the Zendesk and Help Scout refugees) as a second segment.

### 2.2 Market viability
- **Demand:** Shopify has millions of merchants. Your target is the **tens of thousands of stores doing about $500K–$20M per year** that handle 300–5,000 tickets a month. That is large enough to need a help desk and small enough to resent enterprise pricing.
- **Customers needed:** ~110 stores at ~$180 ARPA = **$20K MRR**. That is a tiny share of the segment.
- **Growth:** Support work is moving to AI agents quickly, which keeps forcing merchants to re-evaluate tools. Every re-evaluation is a switching moment you can catch.

### 2.3 Competitive landscape

| Competitor | Pricing model (2026) | Strength | Weakness to exploit |
|---|---|---|---|
| **Gorgias** | $10–750/mo by ticket volume (50–5,000 tickets) + $0.90–1.00 per AI resolution, which also counts as a ticket; $1.50 overage | Category leader for Shopify, deep integration | Double billing, unpredictable bills, AI is Shopify-only |
| **Zendesk** | $55–115/agent + $1.50–2.00 per AI resolution; overages auto-billed since Jan 2026 | Brand, breadth | Expensive, complex, generic (not e-commerce-native) |
| **eDesk** | ~$39/agent | Marketplace sellers (Amazon, eBay) | Per-seat |
| **Richpanel** | From ~$29/mo | Self-service portal, returns | Narrower help desk |
| **Re:amaze** | Per-seat | Multi-brand shared inbox | Per-seat, less AI-forward |
| **Chatwoot Cloud** | $19–99/agent + AI credits | Open source | Per-agent, not e-commerce-specific |
| **AI-only add-ons** (many startups) | Per resolution or subscription | Fast-moving AI | Not a full help desk, so merchants still pay for one |

**Your position:** a *complete* help desk with an AI agent included, priced **by store size, not by tickets, agents or resolutions.**

### 2.4 Revenue model and unit economics

| Plan | Price/mo | Included | Target store |
|---|---|---|---|
| Starter | **$79** | Unlimited agents, 750 tickets, 300 AI resolutions | < $1M/yr revenue |
| Growth | **$199** | Unlimited agents, 3,000 tickets, 1,500 AI resolutions | $1–5M |
| Scale | **$449** | Unlimited agents, 8,000 tickets, 4,000 AI resolutions, multi-store | $5–20M |
| AI overage | **$0.20 / resolution** | | 4–10× cheaper than incumbents |

**Cost comparison for a $3M store** handling about 2,000 tickets a month, with roughly 1,000 resolved by AI:
- Gorgias: a ticket plan covering 2,000+ tickets, **plus** about $900–1,000 in AI fees, with AI resolutions also counting as tickets. Realistically **$1,000+/mo**.
- Zendesk: 4 agents × $55 = $220, **plus** about 1,000 × $1.50 = $1,500. About **$1,700/mo**.
- **You: $199/mo.** A pricing calculator showing this gap is your main sales page.

**Cost to serve (estimates; benchmark them yourself):**
- **AI:** One resolution is roughly 3–6 LLM calls with retrieval. On current small and mid-sized models that is about **$0.01–0.05 per resolution**. At the worst case of $0.05, 1,500 AI resolutions cost about $75. Design the AI pipeline (cheap model first, escalate to a bigger one only when needed) to keep the average under $0.02.
- **Infrastructure:** Chatwoot is **natively multi-tenant** (each customer is an "account" in one installation), so 100+ customers can share one deployment (Rails + Sidekiq + Postgres + Redis). Budget about $300–700/mo of infrastructure at 100 customers.
- **Email:** Postmark or SES, a few dollars per customer.
- **Blended gross margin:** about **75–85%**.

**LTV:** $180 × 80% ÷ 2.5% monthly churn ≈ **$5,760**. Help desks are sticky because ticket history and macros live there.

### 2.5 Product: what to build on Chatwoot's MIT core

**You inherit from Chatwoot:** a multi-tenant account model, a shared inbox, email, live-chat widget, WhatsApp/Facebook/Instagram/Telegram channels, contacts, labels, canned responses, teams, reports, Platform and Account APIs, and a Vue agent UI.

**You must not use:** anything under `enterprise/`. Rebuild what you need there (SLA, audit logs, its AI features) yourself. Remove Chatwoot's name and logo; trademark law still applies even under MIT. Keep the MIT notice in a `THIRD_PARTY_LICENSES` file and on an in-app licenses page.

**What you build (your differentiation and your closed-source code):**

| # | Feature | Why it wins |
|---|---|---|
| 1 | **Shopify app** (OAuth, embedded admin, Billing API) | Distribution and payments |
| 2 | **Order sidebar**: customer's orders, fulfillment and tracking next to every ticket | Table stakes, and it saves agents time |
| 3 | **AI agent with actions**: where's my order, cancel, edit address, start return, discount code, all with merchant-set policies and limits | This is what merchants pay for |
| 4 | **"Approve before send" mode** for AI | Builds trust during onboarding |
| 5 | **One-click importers** from Gorgias, Zendesk, Re:amaze and Help Scout (tickets, macros, customers) | Removes the switching cost |
| 6 | **Macro → AI training**: turn existing macros and past tickets into the AI's knowledge base automatically | Live in minutes instead of weeks |
| 7 | **ROI dashboard**: "AI resolved 1,214 tickets this month; Gorgias would have charged you $1,092" | Prevents churn |
| 8 | Shopify-themed reskin of the UI, mobile app later | Polish |

**Stack:** Chatwoot (Ruby on Rails, Vue, Postgres, Redis, Sidekiq) + a separate **AI service** (Python or TypeScript; LLM API; pgvector for retrieval) + Shopify app (Remix/Node template) + Postmark/SES + Hetzner/Render/AWS.

Keep the AI service and Shopify app in **separate repos you own outright**. Keep your Chatwoot fork thin so you can still merge upstream updates.

### 2.6 Twelve-week build plan (1 full-stack developer + you)

| Week | Deliverable |
|---|---|
| 1 | Fork Chatwoot, strip `enterprise/`, rebrand, deploy one multi-tenant production environment |
| 2–3 | Shopify app: OAuth, account provisioning through the Platform API, Billing API plans |
| 4–5 | Order sidebar and Shopify webhooks (orders, fulfillments, customers) |
| 6–8 | AI agent v1: retrieval over macros, help center and past tickets; actions (order status, cancel, address edit); approval mode |
| 9 | Gorgias importer (the biggest source of switchers), then Zendesk |
| 10 | ROI dashboard, onboarding checklist, cost calculator on the website |
| 11 | Beta with 5–10 stores; fix whatever breaks |
| 12 | Shopify App Store submission and public launch |

### 2.7 Go-to-market

1. **Shopify App Store optimization.** Target keywords "helpdesk", "AI customer service", "Gorgias alternative". **Reviews drive ranking**: get the first 20 reviews from beta users with white-glove onboarding.
2. **Gorgias-switcher campaign.** Comparison pages, a calculator ("paste your last Gorgias invoice"), and a free migration done by you. Time outreach to Q4 renewals and post-BFCM ticket spikes, when Gorgias bills peak.
3. **Shopify agency partners.** Shopify and Shopify Plus agencies set up their clients' tool stacks. Offer 20–30% recurring revenue share. Five good agencies can bring 30+ stores.
4. **Communities.** r/shopify, r/ecommerce, DTC Slack groups, ecommerce founder X/LinkedIn. Post real ROI screenshots from beta stores.
5. **Content and SEO.** Write about "Gorgias pricing", "Zendesk AI cost per resolution" and "how to automate WISMO" (where-is-my-order tickets). Incumbents' price confusion drives search volume.
6. **Founder-led sales for the first 30 stores.** Book demos and migrate their data yourself.

### 2.8 Path to $20K MRR

| Phase | Months | Milestone | MRR |
|---|---|---|---|
| Validate | 0–1 | Interview 25 store owners, collect 10 Gorgias/Zendesk invoices, get 5 paid beta commitments | $0 |
| Build | 1–3 | 12-week plan above | ~$500 (beta) |
| Launch | 3–6 | App Store live, 20 reviews, 2 agency partners | ~$5K (~30 stores) |
| Grow | 6–12 | Zendesk/Help Scout importers, agency program, BFCM push | ~$12K (~70 stores) |
| Target | 12–18 | Scale plan and multi-store, B2B segment launch | **~$20K (~110 stores)** |

**KPIs:** AI resolution rate ≥ 40% within 30 days of install · logo churn ≤ 2.5%/mo · install→paid ≥ 25% · AI cost ≤ 8% of revenue · App Store rating ≥ 4.8.

**Kill criteria:** If by month 6 you have fewer than 15 paying stores, or the AI resolution rate stays below 25%, re-segment to B2B SaaS support or pivot to idea #2.

### 2.9 Budget (bootstrapped)

| Item | Months 0–6 | Months 6–18 |
|---|---|---|
| Developer (contract or co-founder) | Main cost; varies by region | Same + part-time support |
| Infrastructure | $100–300/mo | $300–700/mo |
| LLM API | $50–200/mo | Scales with usage (target ≤ 8% of revenue) |
| Shopify Partner, Postmark, tools | ~$100/mo | ~$300/mo |
| Marketing (agency rev share is paid from revenue) | ~$500/mo | $1–3K/mo |

Note: Shopify takes a revenue share on app billing. Check the current Shopify Partner terms; small developers typically keep most of their first $1M.

### 2.10 Risks and mitigation

| Risk | Likelihood | Mitigation |
|---|---|---|
| Gorgias or Zendesk switch to flat AI pricing | Medium | It would cannibalize their revenue, so they'll move slowly. Compete on speed, migration and Shopify depth |
| AI takes a wrong action (bad refund or cancel) | Medium | Merchant-set limits, approval mode, full action log, undo where possible |
| Crowded "AI support" space | High | Be the *complete help desk* with flat pricing, not another add-on. Own the Gorgias-switcher niche |
| Chatwoot upstream changes break your fork | Medium | Thin fork, separate AI and Shopify services, merge upstream monthly |
| LLM costs spike or a provider has an outage | Low | Multi-provider abstraction, small-model-first routing, caching |
| Seasonal ticket spikes (BFCM) blow through limits | High | Seasonal overage pricing and auto-scaling infrastructure |
| Shopify platform or policy changes | Low–Medium | Add the B2B email segment by month 12 to diversify |

---

## 3. #2 Option: White-Label Automation Platform for Agencies (Activepieces, MIT)

- **Evidence of demand:** n8n went from about $7M ARR in 2024 to **$100M ARR in April 2026**, and Zapier has about **100K paying customers**. Automation is booming.
- **The gap:** n8n's Sustainable Use License **forbids white-label resale**, so agencies building automations for clients can't rebrand it. Zapier is expensive per task.
- **The product:** A white-label automation platform on **Activepieces' MIT core** (not `packages/ee`). Agencies get their own domain and logo, client workspaces, client-level billing and usage caps, AI-agent steps, and templates per vertical.
- **Pricing:** $149–499/mo per agency with flat client workspaces. You need about 70 agencies.
- **GTM:** The automation-agency community on YouTube, Skool groups and the Make/n8n creator ecosystem, plus template marketplaces.
- **Risks:** Integration maintenance burden (APIs keep changing), Activepieces itself selling embedding, and support load. Higher technical difficulty than #1.

---

## 4. Decision

1. **Build #1** (flat-priced AI help desk for Shopify on Chatwoot's MIT core). It has the clearest pricing gap, a marketplace for distribution, sticky customers, and a legal base you can close-source and rebrand.
2. Keep **#2** as the alternative if you're stronger in automation and integrations than in e-commerce.
3. **Before any code:** collect 10 real Gorgias or Zendesk invoices from store owners. If the cost gap is as large as the math above suggests, you have a business.

---

## Sources

- Zendesk pricing: [eesel.ai](https://www.eesel.ai/blog/zendesk-plans-and-pricing), [voiceflow.com](https://www.voiceflow.com/blog/zendesk-pricing)
- Zendesk AI per-resolution pricing: [corepiper.com](https://corepiper.com/blog/zendesk-ai-agent-pricing-2026/), [eesel.ai](https://www.eesel.ai/blog/a-complete-guide-to-zendesk-ai-agents-setup-costs-and-best-practices)
- Help Scout pricing: [eesel.ai](https://www.eesel.ai/blog/helpscout-pricing), [costbench.com](https://costbench.com/software/help-desk/helpscout/)
- Intercom Fin pricing: [getmacha.com](https://www.getmacha.com/blog/intercom-fin-ai-explained), [wonderchat.io](https://wonderchat.io/blog/intercom-fin-pricing)
- Gorgias pricing and AI double billing: [chatarmin.com](https://chatarmin.com/en/blog/gorgias-pricing), [eesel.ai](https://www.eesel.ai/blog/gorgias-plans-comparison), [getmacha.com](https://www.getmacha.com/blog/gorgias-pricing-explained)
- Shopify help desk landscape: [edesk.com](https://www.edesk.com/blog/shopify-customer-service-apps/), [chatbase.co](https://www.chatbase.co/blog/gorgias-alternatives)
- Chatwoot license, pricing and multi-tenancy: [getmacha.com](https://www.getmacha.com/blog/what-is-chatwoot), [eesel.ai](https://www.eesel.ai/blog/chatwoot-pricing), [deepwiki.com](https://deepwiki.com/chatwoot/chatwoot/3.1-account-and-multi-tenancy)
- FreeScout license and existing hosting: [GitHub](https://github.com/freescout-help-desk/freescout), [getmacha.com](https://www.getmacha.com/blog/what-is-freescout)
- Activepieces license: [GitHub LICENSE](https://github.com/activepieces/activepieces/blob/main/LICENSE), [activepieces.com](https://www.activepieces.com/open-source)
- n8n revenue and license: [sacra.com](https://sacra.com/c/n8n/), [okara.ai](https://okara.ai/blog/n8n-revenue-growth), [ssdnodes.com](https://www.ssdnodes.com/learn/n8n-sustainable-use-license-explained)
- Zapier revenue and pricing: [getlatka.com](https://getlatka.com/companies/zapier), [parseur.com](https://parseur.com/blog/zapier-stats), [tinycommand.com](https://tinycommand.com/blogs/zapier-pricing-explained)
- Canny pricing and flat-rate alternatives: [produktly.com](https://produktly.com/pricing/canny), [productlift.dev](https://www.productlift.dev/blog/canny-pricing/)
- Statuspage pricing and alternatives: [oneuptime.com](https://oneuptime.com/blog/post/2026-03-10-best-statuspage-alternatives/view), [openstatus.dev](https://www.openstatus.dev/guides/how-openstatus-compares-to-other-status-page-tools)
- Mailchimp 2026 changes: [audienceful.com](https://www.audienceful.com/blog/mailchimp-price-increase), [benchmarkemail.com](https://www.benchmarkemail.com/blog/mailchimp-pricing/)
- Hosted listmonk: [xcloud.host](https://xcloud.host/listmonk-hosting/), [opsily.com](https://opsily.com/hosting/listmonk)
- Plausible revenue: [plausible.io](https://plausible.io/blog/open-source-saas), [tinyempires](https://tinyempires.substack.com/p/inside-a-tiny-empire-plausible-analytics)
- License compliance in due diligence: [livmo.com](https://livmo.com/blog/open-source-license-diligence/), [Wikipedia: open source license litigation](https://en.wikipedia.org/wiki/Open_source_license_litigation), [appsecsanta.com](https://appsecsanta.com/sca-tools/open-source-license-compliance)
