# SaaS Idea Catalogue: Proven Products with Open-Source Starting Points

Researched October 2026. This catalogue screens **22 categories** where a proven paid product has an open-source alternative, scores each one, and profiles the best five. Prices and events come from the sources listed at the end. Recheck them before you commit money, because prices in several of these categories changed in the last few months.

Rules used throughout (from the earlier docs):
- Only **MIT/Apache** code can be used freely as a closed, rebranded base.
- **AGPL code** can be a hosted product only if you publish your changes. Otherwise use the product as a reference and write your own code.
- **Source-available code** (Sustainable Use, ELv2, BSL and similar) is off-limits as a base.

---

## 1. The Biggest Finding: "Displacement Events" Are the Best Timing Signal

The easiest customers to win are ones who were just given a reason to leave. Right now, the clearest source of those reasons is **Bending Spoons**, an acquirer whose pattern is to buy established apps, then raise prices and cut staff:

| Product | What happened | Who is now shopping |
|---|---|---|
| **Harvest** (time tracking + invoicing) | 2026 pricing moved to per-seat **plus usage fees** (projects, clients, tasks, invoices). One customer's renewal went from **$211 to $2,548 (12×)**. Bloomberg covered the backlash on Aug 13, 2026 | Agencies, consultancies, freelancers |
| **Airtable** (500K+ organizations) | Acquisition announced Aug 4, 2026 for $1.285B; **closed Sep 4, 2026** | Ops teams nervous about the next renewal |
| **Vimeo** | Acquired late 2025; most of the staff, reportedly including the video team, laid off; small businesses saw notices of **up to 2,400%** increases; forced plan migrations in 2026 | Small businesses, course creators, agencies |
| **Eventbrite** | Acquired; organizer fees of 3.7% + $1.79 per ticket, plus 2.9% processing | Event organizers |
| **Miro** ($600M ARR, 250K customer organizations) | Acquisition agreed Sep 10, 2026; **expected to close Q4 2026** | Watch list: price changes likely in 2027 |

Other displacement events found:
- **Atlassian Data Center end of life:** no new customer sales after March 30, 2026; read-only on March 28, 2029. Self-hosted Jira and Confluence customers must move.
- **License changes:** NocoDB moved from AGPL to a Sustainable Use license in early 2026. tldraw's SDK now needs a paid license for production. Grafana OnCall OSS was archived in March 2026.
- **Price hikes:** Slack Business+ went to $15/user. Mailchimp cut its free plan to 250 contacts. Zendesk now auto-bills AI overages.

**Takeaway:** Build where customers are being pushed out *now*, then make migration effortless.

---

## 2. Master Scoring Table (22 Categories)

**Weights:** Pain and urgency 25% · Open-source base fit 15% (MIT/Apache, and no strong first-party cloud) · Room against competitors 15% · Unit economics 15% · Ease of reaching buyers 20% · Ease of building 10%.

| Rank | Opportunity | Incumbent pain (2026) | Open-source base / license | Score |
|---|---|---|---|---|
| **1** | **Harvest-refugee time tracking + invoicing** | Usage fees on top of seats; 12× renewals | Kimai and Solidtime are AGPL, so **write your own** (small domain) | **4.10** |
| **2** | **Church management** (vs. Planning Center) | Modular pricing: each module from $15/mo; typical church pays $60–400+/mo | **ChurchCRM (MIT)**; Rock RMS is *not* open source (faith-org-only license) | **3.80** |
| **3** | **Flat-priced AI help desk** (vs. Gorgias, Zendesk) | Zendesk $55–115/agent + $1.50–2.00 per AI resolution; Gorgias $0.90–1.00/AI resolution, also billed as a ticket | **Chatwoot core (MIT)**. See `deep-dive-clone-plan.md` | **3.75** |
| **4** | **Flat-priced enterprise SSO/SCIM for B2B SaaS** (vs. WorkOS) | **$125 per SSO connection per month**, and the same again for directory sync | **Keycloak (Apache)** | **3.70** |
| **5** | **Airtable-refugee database** | $20–45/seat, record limits, now owned by Bending Spoons | **Baserow core (MIT)**, Grist core (Apache; verify). NocoDB is now source-available, so it's out | **3.60** |
| 6 | White-label automation for agencies (vs. Zapier, n8n) | Zapier per-task pricing; n8n's license bans white-label resale | Activepieces core (MIT) | 3.20 |
| 7 | Studio/gym management (vs. Mindbody) | $99–699/location + 20% marketplace commission; opaque renewals | No good permissive base, so write your own | 3.20 |
| 8 | Event ticketing (vs. Eventbrite) | 3.7% + $1.79/ticket + 2.9% processing | Hi.Events and pretix are AGPL (comply or write your own) | 3.10 |
| 9 | Self-hosted-friendly wiki for the Confluence Data Center exodus | Forced to cloud by 2029 | BookStack (MIT); Docmost is AGPL | 3.10 |
| 10 | Customer messaging (vs. Customer.io, Braze) | Customer.io $100–1,000/mo; Braze ≈ $1K/mo at 50K MAU | Dittofeed (MIT), but it has its own $75 cloud | 3.05 |
| 11 | Private AI workspace for regulated SMBs | ChatGPT Business is now cheap ($20/user after an April 2026 cut) | LibreChat (MIT) | 3.00 |
| 12 | On-call and incident management | PagerDuty $21–41/user; Grafana OnCall OSS archived | OneUptime (Apache) | 3.00 |
| 13 | Internal tools (vs. Retool) | $7–18 per *end user* on top of builders | Appsmith (Apache) | 2.90 |
| 14 | Site search (vs. Algolia) | Pay per search and per record | Meilisearch (MIT), but its own cloud starts at $20 | 2.85 |
| 15 | Feature flags (vs. LaunchDarkly) | Median contract about $64K/yr | GrowthBook, Unleash, Flagsmith: already the cheap options | 2.85 |
| 16 | Video hosting (vs. Vimeo) | Up to 2,400% increases | No good base; build on Bunny Stream-style infrastructure | 2.80 |
| 17 | Team chat (vs. Slack) | Business+ $15/user | Rocket.Chat core (MIT, check its user limits), Zulip (Apache) | 2.65 |
| 18 | E-signature (vs. DocuSign) | Envelope caps, $25–40/user | DocuSeal and OpenSign are AGPL; DocuSeal cloud is $0.20/doc | 2.65 |
| 19 | Forms (vs. Typeform) | 100 responses on the $28 Basic plan; forms close at the cap | OpnForm, HeyForm and Formbricks are all AGPL; Tally is cheap | 2.60 |
| 20 | Whiteboard (vs. Miro, after the acquisition closes) | Not yet, but expected | Excalidraw (MIT); tldraw is no longer free for production | 2.55 |
| 21 | Observability (vs. Datadog) | Bills for hosts, metrics, logs and APM separately | SigNoz core (MIT), but heavy infrastructure and strong first-party clouds | 2.55 |
| 22 | Scheduling (vs. Calendly) | $10–20/seat | Cal.com is AGPL and already the cheap cloud | 2.25 |

**Why some well-known clones rank low:** In scheduling, forms, e-signature, search, feature flags and analytics, the open-source project's own company already sells the cheap hosted version. You would be the third-cheapest option.

---

## 3. Top 5 Profiles

### #1: Price-Locked Time Tracking and Invoicing for Agencies (the Harvest exodus)

**Market viability**
- **Urgency:** Harvest's new model (seats + usage fees) is producing 5–12× renewal bills. Every Harvest customer reaches a renewal date within 12 months, so the switching window is open *now* and runs through 2027.
- **Buyers:** Agencies, studios and consultancies with 5–50 people who bill by the hour. They were happy, loyal Harvest customers, which means they are proven payers who just need a safe home.

**Competitive landscape**

| Competitor | Strength | Weakness |
|---|---|---|
| Toggl Track | Brand, good timer | Per-seat pricing; invoicing is weak |
| Clockify | Generous free tier | Feels generic; upsells; weaker invoicing |
| Everhour, Productive, Scoro | Agency features | Heavier and pricier |
| Kimai Cloud (AGPL) | Open source | Dated UX; not aimed at Harvest refugees |
| Wave of "Harvest alternative" startups | Fast | Most only have a landing page; few do full migration |

**Product differentiation**
1. **Contractual price lock:** "Your price can't rise more than 5%/year for 3 years." It directly answers the fear these customers now have.
2. **One-click Harvest migration** through Harvest's API: clients, projects, tasks, rates, full time history **and past invoices**.
3. **A familiar workflow:** timer, weekly timesheet, approvals, budgets, invoices from tracked time. Design your own UI that feels familiar, but don't copy Harvest's.
4. **Flat team pricing** with no usage fees, and a profitability dashboard (budget vs. actual per project).
5. Integrations agencies rely on: QuickBooks, Xero, Stripe, Asana, Jira, Linear, Slack, and a browser timer extension.

**Revenue model:** Solo $12 · Team $49 (≤ 8 users) · Studio $119 (≤ 25) · Agency $249 (≤ 60). Expected ARPA is about $90, so you need **~220 customers**. Time data and invoice history are sticky, so expect churn under 2%.

**Operational considerations:** A small domain, so **write it yourself**. That avoids the AGPL issue with Kimai and Solidtime.
- **Stack:** Next.js or Rails, Postgres, Stripe, a browser extension, QuickBooks/Xero APIs.
- **Team:** One full-stack developer can ship a migration-ready MVP in **6–8 weeks**.

**Go-to-market**
- Target "Harvest alternative" and "Harvest price increase" searches, plus a renewal calculator ("paste your Harvest renewal quote").
- Reddit (r/agency, r/freelance, r/webdev), agency-owner communities, and agency newsletters and podcasts.
- Reply directly to people complaining publicly about their renewal.
- Partner with agency-operations consultants.

**Risks**
- Harvest reverses its prices → mitigate by positioning as "the agency profitability tool", not only as an escape route.
- Clockify's free tier → agencies pay for invoicing, migration and a calm vendor.
- The window closes → speed matters; ship in weeks, not months.

**Time to $20K MRR:** about **9–15 months** if launched by early 2027.

---

### #2: All-in-One Church Management at a Flat Price (vs. Planning Center)

- **Market:** Tens of thousands of churches in the US alone, a recurring need (people, giving, check-in, groups, volunteers, events), and **very low churn**. Churches rarely switch once settled.
- **Pain:** Planning Center charges per module (from $15/mo each, scaling with size). A growing church typically pays $60–400+/mo across modules.
- **Open-source base:** **ChurchCRM (MIT)** is a usable starting point or reference (PHP). **Rock RMS is not open source**: its license restricts use to faith-based organizations and it isn't a resale base.
- **Competitors:** Planning Center (free People module, modular paid apps), Breeze, ChurchTrac, Tithe.ly, Pushpay, and Rock RMS with hosting from about $50–200/mo.
- **Wedge:**
  - **One flat price by attendance**, everything included (check-in kiosks, groups, volunteer scheduling, SMS, giving).
  - Migration from Planning Center and Breeze.
  - A simple mobile app for members.
  - **Giving revenue:** a small markup on payment processing adds a second revenue stream that grows with the church.
- **Revenue model:** $49 (< 150 attendees) · $99 (< 500) · $199 (< 1,500), plus giving fees. Expected ARPA is about $90 plus processing. You need **~200 churches**, with churn around 1%/mo.
- **Go-to-market:** Church-admin Facebook groups, denominational networks (one regional network can bring dozens of churches), church-tech conferences, church consultants and web designers as partners, and "Planning Center pricing" searches.
- **Risks:** Slow, committee-driven purchasing (mitigate with a free trial plus white-glove migration), and payments compliance (use Stripe Connect and avoid holding funds).
- **Fit:** Less exciting than tech categories, but buyers are non-technical (so they won't self-host), loyal, and underserved by modern UX.

---

### #3: Flat-Priced AI Help Desk (vs. Gorgias, Zendesk)

Fully analyzed in **`deep-dive-clone-plan.md`**. In short:
- Built on **Chatwoot's MIT core**, which is natively multi-tenant.
- Targets Shopify stores first, priced flat at $79–449/mo with AI resolutions included.
- Incumbents charge $0.90–2.00 per AI resolution.
- Needs about 110 stores at roughly $180 ARPA.

---

### #4: Flat-Priced Enterprise SSO/SCIM for B2B SaaS (vs. WorkOS)

- **Pain:** WorkOS charges **$125 per SSO connection per month**, and the same for directory sync. A SaaS company with 50 enterprise customers pays about $8K–11K/month. Every B2B SaaS company moving upmarket hits this.
- **Open-source base:** **Keycloak (Apache 2.0)**. It is the standard identity server, with SAML, OIDC, organizations and SCIM extensions.
- **Competitors:**
  - SSOJet (about $49 flat) and Scalekit (about $60/connection) are already undercutting WorkOS.
  - Managed-Keycloak hosts (Skycloak, Cloud-IAM, Phase Two) and Ory/Polis.
  - Auth0 B2B, which is expensive and caps enterprise connections on lower tiers.
- **Wedge:**
  - **Unlimited connections per app at a flat price.**
  - A polished self-serve **admin portal** your customers' IT teams use to configure their own SSO (this is the part founders actually pay for).
  - SDKs for popular frameworks and audit logs.
  - A "WorkOS-compatible" migration path.
- **Revenue model:** $149 (≤ 10 connections) · $349 (≤ 50) · $799 (unlimited, SLA). Expected ARPA is about $300, so you need **~70 customers**.
- **Ops:** Keycloak is heavy (Java). Run multi-tenant clusters well, because security and uptime *are* the product. You'll need SOC 2 early.
- **Go-to-market:** SaaS founder communities, "WorkOS pricing" searches, and "enterprise-ready" content.
- **Risks:** Security incidents, and a crowding field. Only pursue this if you have identity or security engineering depth.

---

### #5: The Airtable Exodus

- **Pain and timing:** Bending Spoons closed the acquisition on Sep 4, 2026. Airtable has 500K+ organizations, pricing of $20–45/seat, and record limits. Its new owner has a track record of large price changes.
- **Open-source base:** **Baserow core (MIT)**, or **Grist core (Apache 2.0; verify)**. NocoDB moved to a source-available license in early 2026 and is no longer usable as a base.
- **Competitors:** **Already crowded.** Baserow ($10–18/user cloud), Softr and Noloco are actively marketing to Airtable users, plus Teable, SeaTable and SmartSuite.
- **The winning wedge is technical: an Airtable-compatible API.** Existing Zapier and Make automations, scripts and integrations would keep working after migration. That removes the biggest hidden switching cost, and most competitors don't offer it.
- **Alternative wedge:** Go vertical, for example "Airtable for production companies" or "for recruiting agencies", with templates and workflows built in.
- **Revenue model:** $15/seat or flat team plans. You need about 200–300 teams.
- **Risks:** A spreadsheet-database is hard to build well, funded competitors are moving fast, and Airtable may not raise prices.
- **Verdict:** High potential but hard. Consider it only with strong engineering and a vertical focus.

---

## 4. Watch List (Opportunities Opening Later)

| Trigger | When | What to prepare |
|---|---|---|
| Miro acquisition closes | Q4 2026 → pricing changes likely in 2027 | A focused whiteboard for one use case (retrospectives, workshops, education) on **Excalidraw (MIT)**, plus a Miro board importer |
| Atlassian Data Center end of life | Sales to existing customers end March 2028; read-only March 2029 | A private, self-hostable wiki and issue tracker with Confluence/Jira importers (BookStack MIT as a wiki base). Long enterprise sales cycles |
| Eventbrite under new ownership | Ongoing | A vertical ticketing tool (run clubs, schools, conferences) with flat fees |
| Vimeo forced upgrades | Ongoing in 2026 | Video hosting for course creators or agencies on commodity streaming infrastructure |
| Grafana OnCall OSS archived | Since March 2026 | Hosted on-call for orphaned OnCall users (OneUptime Apache as a base) |

---

## 5. How to Keep Finding Ideas Like These (Repeatable Method)

1. **Track acquirers known for price increases.** Bending Spoons is the main one, plus private-equity rollups. Every acquisition announcement is a 6–18 month lead.
2. **Track license changes and archived repos.** When a project goes source-available or gets archived, its users go looking for a new home. Sources: GitHub "archived" notices, Hacker News, r/selfhosted.
3. **Track pricing-model changes.** Moves to usage, per-resolution or per-contact pricing are often painful. Sources: SaaS pricing trackers and G2/Capterra reviews filtered for "price".
4. **Track end-of-life announcements.** Data Center and on-prem retirements push customers to choose a new tool.
5. **Validate fast.** Check search demand for "[product] alternative", find 20 public complaints, and get 5 pre-payments before writing code.

---

## 6. Recommendation

1. **Fastest path to revenue: #1, Harvest-refugee time tracking and invoicing.** Demand is urgent and dated, the product is small enough to write yourself, buyers have already proven they pay, and migration is the wedge. Start this week, because the window closes as renewals pass.
2. **Most durable business: #2, church management.** Low churn, non-technical buyers and giving revenue, but slower sales.
3. **Highest revenue per customer: #3 (help desk) or #4 (SSO).** These fit a stronger engineering team.

My suggested next step: run the Section 5 validation on #1 within 7 days. Collect 10 real Harvest renewal quotes and 5 pre-payments. If that works, ship the MVP in 6–8 weeks.

---

## Sources

- **Bending Spoons and Harvest:** [Bloomberg, Aug 13, 2026](https://www.bloomberg.com/news/articles/2026-08-13/bending-spoons-customers-decry-outrageous-app-price-hikes), [silicon.co.uk](https://www.silicon.co.uk/e-enterprise/merger-acquisition/harvest-price-shock-631249), [tillage.ai](https://tillage.ai/blog/harvest-price-increase-2026), [clockify.me](https://clockify.me/blog/apps-tools/harvest-pricing/), [clawnify.com](https://www.clawnify.com/resources/bending-spoons-acquisitions)
- **Airtable acquisition:** [TechCrunch](https://techcrunch.com/2026/08/04/bending-spoons-to-buy-airtable-for-1-28b/), [Yahoo Finance](https://finance.yahoo.com/markets/stocks/articles/bending-spoons-completes-acquisition-airtable-123000421.html), [stocktitan.net](https://www.stocktitan.net/news/BSP/bending-spoons-completes-the-acquisition-of-s9a0tyer611r.html), [Softr](https://www.softr.io/blog/airtable-bending-spoons-acquisition)
- **Miro acquisition:** [Bending Spoons IR](https://investors.bendingspoons.com/newsroom/bending-spoons-agrees-to-acquire-miro), [The Next Web](https://thenextweb.com/news/bending-spoons-miro-acquisition-1-355bn)
- **Vimeo:** [PetaPixel](https://petapixel.com/2026/09/04/vimeo-can-decide-to-change-your-plan-and-force-you-to-pay-5x-more/), [Livid](https://livid.com/blog/vimeo-price-increase-2026-a-complete-breakdown-of-the-new-plans-and-forced-upgrades/), [netinfluencer.com](https://www.netinfluencer.com/after-vimeo-price-hikes-livid-courts-creators-and-small-businesses-weighing-alternatives/)
- **Eventbrite and ticketing:** [TechCrunch](https://techcrunch.com/2026/06/08/eventbrite-and-vimeo-owner-bending-spoons-files-to-go-public/), [Ticket Tailor](https://www.tickettailor.com/blog/bending-spoons-acquires-eventbrite-what-does-this-mean), [Hi.Events fees](https://hi.events/fees), [Hi.Events vs pretix](https://hi.events/compare/eventbrite-vs-pretix)
- **Time tracking bases:** [Kimai (GitHub)](https://github.com/kimai/kimai), [chronoid.app](https://www.chronoid.app/blog/open-source-time-tracking-software)
- **Church management:** [ChurchCRM](https://churchcrm.io/), [theleadpastor.com](https://theleadpastor.com/tools/best-open-source-church-management-software/), [churchmemberpro.com](https://churchmemberpro.com/blog/planning-center-alternatives/), [Rock RMS review](https://churchmemberpro.com/blog/rock-rms-review/), [spotsaas.com](https://www.spotsaas.com/compare/planning-center-vs-rock-rms)
- **SSO/SCIM:** [Scalekit](https://www.scalekit.com/blog/workos-alternatives), [Security Boulevard](https://securityboulevard.com/2026/05/5-best-workos-alternatives-for-b2b-saas-teams-that-need-enterprise-sso-in-2026/), [Skycloak](https://skycloak.io/blog/workos-alternatives-developers/), [SuperTokens](https://supertokens.com/blog/cheapest-auth-alternatives)
- **Auth0:** [auth0pricing.com](https://auth0pricing.com/), [idsync.com](https://idsync.com/guides/auth0-pricing)
- **Airtable alternatives and licenses:** [Baserow pricing](https://baserow.io/pricing), [stackfyi.com](https://www.stackfyi.com/guides/baserow-vs-nocodb-2026), [bitdoze.com](https://www.bitdoze.com/self-hosted-airtable-alternatives/)
- **Help desk:** see sources in `deep-dive-clone-plan.md`
- **Automation:** [sacra.com (n8n)](https://sacra.com/c/n8n/), [Activepieces LICENSE](https://github.com/activepieces/activepieces/blob/main/LICENSE)
- **Mindbody:** [Koalendar](https://koalendar.com/blog/mindbody-pricing-costs), [PushPress](https://www.pushpress.com/blog/7-best-mindbody-alternatives-for-gym-owners-in-2026)
- **Atlassian Data Center end of life:** [getint.io](https://www.getint.io/blog/jira-data-center-end-of-life), [licenseware](https://wiki.licenseware.io/wiki/atlassian-data-center-end-of-life-and-pricing/)
- **Wikis:** [BookStack](https://www.bookstackapp.com/about/confluence-alternative/), [Opsily (Docmost)](https://opsily.com/hosting/docmost/self-hosted-confluence-alternative)
- **Customer messaging:** [OpenAlternative (Braze)](https://openalternative.co/alternatives/braze), [Dittofeed](https://www.dittofeed.com/), [GetVero](https://www.getvero.com/resources/5-best-customer-io-alternatives-in-2026/)
- **AI workspace:** [LibreChat](https://www.librechat.ai/about), [IntuitionLabs (ChatGPT plans)](https://intuitionlabs.ai/articles/chatgpt-plans-comparison)
- **On-call:** [OneUptime](https://oneuptime.com/blog/post/2026-02-06-best-pagerduty-alternatives/view), [runframe.io](https://runframe.io/comparisons/grafana-oncall-alternatives)
- **Internal tools:** [ToolJet blog](https://blog.tooljet.com/retool-alternatives-2026/), [mith.tech](https://mith.tech/blog/open-source-retool-alternative)
- **Search:** [Meilisearch](https://www.meilisearch.com/blog/algolia-alternatives), [buildmvpfast.com](https://www.buildmvpfast.com/alternatives/algolia)
- **Feature flags:** [GrowthBook](https://www.growthbook.io/insights/launchdarkly-pricing)
- **Observability:** [SigNoz](https://signoz.io/blog/open-source-datadog-alternative/), [apiscout.dev](https://apiscout.dev/guides/datadog-vs-signoz-vs-grafana-vs-openobserve-2026)
- **Team chat:** [Slack pricing update](https://slack.com/help/articles/39264531104275-Updates-to-feature-availability-and-pricing-for-Slack-plans), [costbench.com](https://costbench.com/software/communication/slack/), [wz-it.com](https://wz-it.com/en/blog/slack-alternatives-mattermost-rocketchat-zulip/)
- **E-signature:** [PandaDoc](https://www.pandadoc.com/blog/docusign-pricing/), [DocuSeal (GitHub)](https://github.com/docusealco/docuseal), [OpenAlternative](https://openalternative.co/compare/docuseal/vs/opensign)
- **Forms:** [Formbricks](https://formbricks.com/blog/typeform-pricing), [OpenAlternative](https://openalternative.co/alternatives/typeform)
- **Whiteboard:** [meetrix.io](https://meetrix.io/blogs/open-source-whiteboard-tools/)
- **Scheduling:** [Vendr](https://www.vendr.com/marketplace/calendly), [agiled.app](https://agiled.app/alternatives/calendly)
- **Voice AI and chatbots** (screened, not ranked): [particula.tech](https://particula.tech/blog/vapi-vs-retell-vs-livekit-vs-pipecat-voice-agent-platform), [Botpress](https://botpress.com/blog/chatbase-review)
- **Billing** (screened, not ranked): [Lago](https://getlago.com/blog/top-7-alternatives-to-chargebee-for-billing), [rework.com](https://resources.rework.com/tools/billing-revenue/best-chargebee-alternatives)
