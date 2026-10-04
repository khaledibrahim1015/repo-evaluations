# Rawwejly: product requirements (AI marketing team for small businesses)

Oct 4, 2026 · khaled

## Overview

**One subscription gives a small business the marketing work a part-time marketer would do: get found, publish content, stay in touch with customers, and see what worked.** The product checks every week how the business appears on Google and in AI assistants (ChatGPT, Claude, Gemini, Perplexity). It then writes the week's plan with drafts ready, the owner approves in a few minutes, and the product publishes and reports back.

**The problem.** Small business owners know they should do SEO, post on social media and send newsletters, but they lack the time and the know-how. Today they juggle three to five separate tools, each built for marketers, or they pay an agency they can't afford. Meanwhile customers increasingly ask AI assistants for recommendations, and most small businesses have no idea whether they appear there.

**The product promise.** "Spend 15 minutes a week. We tell you what to do, write it for you, publish it, and show you the results."

**What makes it different**

- **A plan, not a toolbox.** Competing tools show data and wait for the user to act. This product decides the next actions and drafts them.
- **AI-search visibility built in.** It tracks whether AI assistants mention and recommend the business, a gap most small-business tools don't cover yet.
- **Plain language everywhere.** Every finding says what is wrong, why it matters and how to fix it, with no jargon.
- **One place for the whole loop.** SEO, content, social, email and results share one brand profile, one calendar and one bill.

**Name (decided): Rawwejly (روّجلي), "promote it for me".** Rawaj (رواج) is not usable: a Saudi app called [Rawaj](https://play.google.com/store/apps/details?id=sa.com.rawaj) already sells marketing to merchants, a Saudi agency uses the name, an Egyptian page runs "رواج للتسويق الرقمي", and rawaj.com, rawaj.ai and rawaj.app are registered. Common Arabic marketing words (Sada, Wasla, Raya, Sayt, Nabda) are also taken as .ai domains, so the name should be a coined or compound word. Check domain, social handles and trademarks in Egypt, Saudi Arabia and the UAE before committing.

**Name shortlist (checked 2026-10-04).** "Domains" means no DNS record was found, which suggests but does not prove the domain is unregistered; confirm at a registrar, and have a lawyer run the trademark search.

| Name | Meaning | Domains (.com / .ai / .io / .app) | Existing companies found | Verdict |
| --- | --- | --- | --- | --- |
| **Zahwa (زهوة)** | The glow and pride of a thriving business | All four look free | An Egyptian contractor and an art gallery, both outside marketing | **Recommended** |
| Nomowly (نمّولي) | "Grow it for me" | All four look free | None found | Good, but in Arabic script it can be misread as "fund me" |
| Rawwejly (روّجلي) | "Promote it for me" | .com, .ai, .io look free | None found | Chosen. Clear meaning; check trademark risk against the Saudi marketing app Rawaj |

Rejected after checking: Mazeedly (Zid's Saudi platform Mazeed), Zabouny (Zbooni, a UAE merchant app), Tarweeji (Tarwege, a WhatsApp marketing tool), Shohra (an Egyptian marketing agency), Khotta (a Saudi planner app).

## Target customers

The first customer is a local or online small business with 1 to 20 staff, a website, and nobody whose job is marketing. Agencies and freelancers are the second customer, because one agency account brings many businesses.

| Persona | Who they are | What they hire the product for | What they fear |
| --- | --- | --- | --- |
| **Sara, the owner-operator** (primary) | Runs a clinic, salon, restaurant, law office or small online shop. Does marketing herself at night. | "Tell me exactly what to do this week, and do most of it for me." More calls, bookings and orders. | Wasting money on tools she can't use; looking unprofessional online. |
| **Omar, the solo marketer** | The one person doing marketing at a 10 to 50 person company. | One tool instead of four; reports he can show his boss. | Missing a channel; spending all day on busywork. |
| **Lina, the small agency owner** | Runs marketing for 5 to 40 client businesses. | Run every client from one login, white-labelled reports, approval flows with clients. | Per-client tool costs eating the margin; clients leaving. |

**Not the target (for now):** large companies with marketing teams, e-commerce brands needing ad management, and businesses without a website.

**First market (decided): Egypt, then Saudi Arabia and the UAE.** Egypt has the most small businesses and the lowest cost to test with; the Gulf pays higher prices. The product is Arabic-first with English alongside. **Who to sell to (decided): both businesses and agencies from launch**, with the Agency plan available on day one.

**Niches (decided): all small businesses.** Onboarding offers ready-made templates for the four biggest groups (clinics, restaurants and cafes, real estate, and online shops), each with its own question set, content ideas and seasonal campaigns. Your own marketing should still focus on one or two of these at a time, because that is cheaper and easier to measure.

## Goals and success metrics

The business goal is paying customers who stay. The product goal is that a customer approves and publishes their weekly plan, because that habit is what keeps them paying. The targets below are starting guesses to revise once real data exists.

| Metric | What it measures | Starting target |
| --- | --- | --- |
| Activation | New signups who finish onboarding and approve their first plan within 7 days | 40% |
| Weekly plan approval | Active customers who approve at least one plan item each week | 60% |
| Time to approve | Median minutes a customer spends reviewing a weekly plan | Under 15 |
| Trial to paid | Trials that convert to a paid plan | 15% |
| Monthly churn | Paying customers who cancel each month | Under 5% |
| Visible results | Customers whose AI-visibility score or Google clicks improve within 90 days | 50% |
| Gross margin | Revenue left after AI, SEO data and email sending costs | Above 70% |

**Non-goals:** becoming a full CRM, an ads manager, or a website builder.

## The weekly loop

The whole product is built around one repeating week. Every feature below exists to make one of these five steps better.

1. **Check.** Scan Google and the AI assistants for how the business appears.
2. **Plan.** Write 3 to 7 tasks for the week, with drafts in the brand voice.
3. **Approve.** The owner edits, skips or approves in about 15 minutes (the only step done by the owner).
4. **Publish.** Blog, social and email go out on schedule.
5. **Measure.** A results email goes to the owner. Results and skipped tasks shape next week's plan.

The product checks and plans on Monday, the owner approves when convenient, and publishing runs on the schedule the plan set. Each week's results and skipped tasks make the next plan sharper.

## Features

Each module lists its features, what each must do, when it ships, and which open source project to study for how it is built. Release labels: **MVP** = first paid version, **v1** = the full core product, **v2** = later, when customers ask.

### 1. Onboarding and brand profile

The brand profile is what makes every draft sound like the business, so onboarding must fill it in without effort from the owner.

| Feature | Requirements | Release | Study |
| --- | --- | --- | --- |
| Sign up and log in | Email and password, Google sign-in, email verification, password reset | MVP | OpenSEO `src/server/auth` |
| Website scan | Owner enters their website; the product reads it and pre-fills business name, services, locations, tone of voice and colours | MVP | Notra `apps/onboarding-agent` |
| Brand profile | Editable fields: what we sell, who we sell to, cities served, tone (friendly, formal…), words to avoid, language (Arabic, English or both), Arabic style (Modern Standard Arabic by default, or Egyptian or Gulf dialect), logo, colours | MVP | TryPost `app/Services/Brand` |
| Competitors | Product suggests 3 to 5 competitors from search results; owner confirms or edits | MVP | OpenSEO `src/server/features/domain` |
| Goals | Owner picks a main goal: more calls, more bookings, more online sales, more newsletter readers | MVP | (your own) |
| Connect Google | Optional connection to Google Search Console and Google Analytics 4 for real click and visit data | v1 | OpenSEO `features/gsc`, `features/ga4` |
| Guided checklist | A setup checklist with progress (connect Google, connect social, import contacts) | MVP | OpenSEO `features/activation` |

### 2. Get found on Google (SEO)

| Feature | Requirements | Release | Study |
| --- | --- | --- | --- |
| Site audit | Crawl up to the plan's page limit; find broken links, missing titles and descriptions, slow pages, missing alt text, duplicate content, mobile issues. Every finding gets: the page, why it matters in one sentence, how to fix it, and a priority | MVP | OpenSEO `features/audit`, RankMeFast `server/src` |
| Health score | One 0 to 100 score with its trend, so the owner sees progress | MVP | RankMeFast |
| Keyword ideas | Suggest keywords the business could rank for, with search volume, difficulty and intent, grouped by topic | MVP | OpenSEO `features/keywords` |
| Rank tracking | Track chosen keywords weekly (daily on higher plans) by country or city, for the business and its competitors | MVP | OpenSEO `features/rank-tracking` |
| Competitor view | Keywords competitors rank for that the business does not | v1 | OpenSEO `features/domain` |
| Backlinks | Count of sites linking to the business vs competitors, with new and lost links | v1 | OpenSEO `features/backlinks` |
| Local SEO | Check Google Business Profile completeness, reviews count and rating, name/address/phone consistency | v1 | Postiz `gmb.provider.ts` (Google Business API) |
| Fix help | For each finding, a copy-paste fix (meta description text, alt text) written by AI | v1 | (your own) |

### 3. Get found in AI assistants (AI visibility)

| Feature | Requirements | Release | Study |
| --- | --- | --- | --- |
| Question set | Generate 20 to 50 questions real buyers ask ("best dentist in Nasr City"); owner can add, edit or import | MVP | Notra `packages/geo-core` |
| AI scans | Ask each question to ChatGPT, Claude, Gemini and Perplexity on a schedule; store the full answers and cited sources | MVP | Notra `apps/api/src/routes/geo-scans.ts`, OpenSEO `features/ai-search` |
| Visibility score | Share of answers that mention the business, its position in recommendation lists, and trend over time, per assistant | MVP | Notra `geo-visibility.ts` |
| Who shows up instead | Competitors mentioned where the business is missing, and the sources the assistants cite | MVP | Notra |
| Content gaps | Turn missing questions into suggested articles or page changes, fed into the weekly plan | v1 | Notra `lib/geo/content-gaps-refresh.ts` |
| AI crawler traffic | A small script on the site shows visits from AI crawlers and referrals from AI assistants | v2 | Notra `packages/geo` |

### 4. Weekly AI plan (the heart of the product)

| Feature | Requirements | Release | Study |
| --- | --- | --- | --- |
| Plan generation | Every Monday, create 3 to 7 tasks ranked by expected impact, using the audit, rankings, AI visibility, the goal, and last week's results | MVP | (your own) |
| Task types | Fix a site issue; publish a blog article; publish social posts; send a newsletter; ask for reviews; update Google Business Profile | MVP (first 3), v1 (rest) | (your own) |
| Ready-made drafts | Each content task arrives with its draft written in the brand voice | MVP | TryPost `app/Services/Ai` |
| Why this task | One sentence per task on why it matters, linked to the data behind it | MVP | (your own) |
| Approve, edit, skip | One-click approve; inline edit; skip with a reason, which teaches the next plan | MVP | (your own) |
| Plan email | The plan arrives by email with an "Approve all" link | v1 | Postiz `digest.email.workflow.ts` |
| Ask the assistant | Chat with an assistant that knows the business data ("why did traffic drop?") | v2 | OpenSEO `features/sam` |

**Seasonal calendar (MVP).** The plan knows the MENA marketing calendar: Ramadan, Eid al-Fitr, Eid al-Adha, back to school, White Friday, Mother's Day (21 March in Egypt) and national days in Egypt, Saudi Arabia and the UAE. It prepares those campaigns two to three weeks ahead, with Hijri dates converted automatically.

### 5. Content studio

| Feature | Requirements | Release | Study |
| --- | --- | --- | --- |
| Blog articles | Brief (topic, keyword, outline) then a full draft of 800 to 2,000 words with headings, FAQ, and internal link suggestions | MVP | Notra `packages/content-generation` |
| Publish to website | One-click publish to WordPress; copy-as-HTML for other sites; later Webflow, Shopify, Wix | MVP (WordPress) | Postiz `wordpress.provider.ts` |
| Social posts | Write per-network versions from one idea or from a published article | MVP | TryPost composer, Postiz |
| Images | Branded image templates (quote, tip, offer) with the logo and colours; optional AI images | v1 | TryPost `app/Services/Image` |
| Carousels | Multi-slide posts for Instagram and LinkedIn | v2 | TryPost AI carousel builder |
| Repurpose | Turn one article into posts, a newsletter section and a short video script | v1 | TryPost `app/Services/Repurpose` |
| Content library | All drafts and published items, searchable, with status | MVP | TryPost asset library |

### 6. Social publishing

| Feature | Requirements | Release | Study |
| --- | --- | --- | --- |
| Connect accounts | OAuth connection with token refresh; start with Facebook Page, Instagram Business and Google Business Profile, the channels MENA small businesses use most; then TikTok, LinkedIn, X, YouTube and Snapchat | MVP (3 networks), v1 (rest) | Postiz `integrations/social/*.provider.ts` |
| Composer | One post, tailored per network with live previews and each network's character and media limits | MVP | TryPost composer |
| Calendar | Month, week and day views; drag to reschedule | MVP | TryPost, Postiz |
| Scheduling and queue | Posts publish at the chosen time; suggested best times; retries on failure; clear error messages when a token expires | MVP | Postiz `orchestrator/workflows/post-workflows`, TryPost `app/Exceptions/Social` |
| Post analytics | Reach, likes, comments, clicks per post and per account | v1 | TryPost post metrics |
| First comment, hashtags, signatures | Saved hashtag groups and sign-offs; optional first comment | v1 | Mixpost, TryPost signatures |

### 7. Email marketing

| Feature | Requirements | Release | Study |
| --- | --- | --- | --- |
| Contacts | Import CSV, add manually, custom fields, tags; deduplicate by email | v1 | Keila `lib/keila/contacts` |
| Sign-up forms | Embeddable form and hosted page; double opt-in; spam protection | v1 | Keila forms, Mautic `FormBundle` |
| Newsletter editor | Block editor with brand colours; AI-written newsletter from the week's content; preview on mobile and desktop | v1 | Keila `mailings/renderer` (MJML, Markdown) |
| Sending | Send now or schedule; send through Amazon SES; rate limiting; per-customer sending domain with SPF, DKIM and DMARC setup guide | v1 | Keila `delivery_worker.ex`, `rate_limiter.ex`, `sender_adapters/` |
| Deliverability protection | Handle hard and soft bounces, spam complaints and unsubscribes automatically; suspend accounts with high complaint rates | v1 | Keila `mailings/message_actions/` |
| Email analytics | Delivered, opens, clicks, unsubscribes per campaign | v1 | Keila `lib/keila/tracking` |
| Segments | Filter contacts by tag, field, or activity | v2 | Dittofeed `backend-lib/src/segments` |

### 7b. WhatsApp campaigns

In Egypt and the Gulf, customers read WhatsApp far more than email, so WhatsApp ships in v1 next to email.

| Feature | Requirements | Release | Study |
| --- | --- | --- | --- |
| Connect WhatsApp Business | Customer connects their own number through the WhatsApp Business Cloud API (Meta); guided setup and template approval | v1 | Dittofeed (has a WhatsApp channel) |
| Broadcasts | Send approved template messages to opted-in contacts; Arabic and English templates; schedule like a newsletter | v1 | Dittofeed messaging, Keila sending logic |
| Opt-in and opt-out | Collect consent through forms and click-to-chat links; honour "stop" replies automatically | v1 | Keila unsubscription handling |
| Cost pass-through | Meta charges per message; show the estimated cost before sending and bill it to the customer, or let them use their own Meta billing | v1 | (your own) |
| Click-to-WhatsApp | Buttons and QR codes for the website and social posts that open a chat | v1 | (your own) |

### 8. Results and reports

| Feature | Requirements | Release | Study |
| --- | --- | --- | --- |
| Home dashboard | This week's plan, health score, AI-visibility score, rankings, social and email results, each with its trend | MVP | OpenSEO `features/dashboard` |
| Weekly results email | What was published, what changed, and one thing that worked | MVP | Postiz digest workflow |
| Monthly report | Shareable PDF and link, with the business's or agency's logo | v1 | OpenSEO `features/reports`, Mautic `ReportBundle` |

### 9. Automations (v2)

| Feature | Requirements | Release | Study |
| --- | --- | --- | --- |
| Ready-made journeys | Welcome series for new subscribers; review request after a visit; "we miss you" for inactive contacts | v2 | Dittofeed `backend-lib/src/journeys`, Mautic `CampaignBundle` |
| Journey builder | Visual steps: trigger, wait, condition, send email, add tag | v2 | Dittofeed journey UI |
| Lead scoring | Points for opens, clicks, form fills; hot leads flagged | v2 | Mautic `PointBundle` |

### 10. Customer inbox (v2)

| Feature | Requirements | Release | Study |
| --- | --- | --- | --- |
| Unified inbox | Website chat widget, email replies, and Facebook and Instagram messages in one list | v2 | erxes `backend/plugins/frontline_api` |
| AI reply suggestions | Draft replies from the brand profile and past answers | v2 | (your own) |
| Review replies | Reply to Google reviews from the inbox | v2 | Postiz `gmb.provider.ts` |

### 11. Teams and agencies

| Feature | Requirements | Release | Study |
| --- | --- | --- | --- |
| Workspaces | One workspace per business; switch between them; data fully separated | MVP | TryPost workspaces |
| Roles | Owner, Admin, Member, Client (approve only) | v1 | TryPost roles, `app/Policies` |
| Client approval links | Send a plan or post to a client to approve without an account | v1 | TryPost approval flows |
| White label | Agency logo, colours and custom domain on reports and approval pages | v2 | (your own) |

### 12. Billing and admin

| Feature | Requirements | Release | Study |
| --- | --- | --- | --- |
| Plans and trial | 14-day free trial, monthly and yearly billing, upgrade and downgrade, invoices; Paymob in Egypt (cards, Meeza, mobile wallets), Stripe through a UAE company for Gulf and international customers; prices in EGP, SAR, AED and USD; VAT invoices for Egypt, Saudi Arabia and the UAE | MVP | OpenSEO `src/server/billing`, TryPost (Laravel Cashier with Stripe) |
| Usage limits | Per-plan limits on pages crawled, tracked keywords, AI questions, AI words, social accounts, emails sent; clear warnings near limits | MVP | OpenSEO billing usage |
| Admin console | See customers, usage and costs; impersonate for support; suspend abusive accounts | MVP | OpenSEO, Keila |
| Data export and deletion | Customer can export all data and delete their account | MVP | OpenSEO `src/server/gdpr` |
| Referral program | Give a month, get a month | v2 | OpenSEO `src/server/referrals` |

## Non-functional requirements

| Area | Requirement |
| --- | --- |
| Security | Social and Google tokens encrypted at rest; passwords hashed; two-factor login on v1; every query scoped to the workspace so one customer can never see another's data |
| Privacy | Follows Egypt's Personal Data Protection Law (No. 151 of 2020), Saudi Arabia's PDPL and the UAE's PDPL, and is GDPR-ready: data export, account deletion, a data processing agreement for customers, cookie consent on the marketing site; email contacts belong to the customer |
| Email compliance | Every newsletter has an unsubscribe link and the sender's postal address; double opt-in available; complaint rate monitored per account (CAN-SPAM, GDPR) |
| Platform rules | Follow each social network's API terms and rate limits; complete app review for Meta, LinkedIn and TikTok before launch of those networks |
| Reliability | Scheduled posts and emails go out within 5 minutes of their time; failed jobs retry with backoff and alert the customer if they still fail; daily database backups |
| Performance | Pages load in under 2 seconds; long jobs (crawls, AI scans, article drafts) run in the background with progress shown |
| Cost control | Track the cost of every AI call, SEO data request and email per workspace; plan limits keep each plan above the gross-margin target; cache SEO data shared between customers |
| AI quality | Drafts never invent prices, offers or claims not in the brand profile; banned words respected; every AI output reviewed by the customer before publishing |
| Languages | Arabic and English from the first release: full right-to-left layout, Arabic fonts, Arabic keyword research and AI-visibility questions, and AI drafts in the dialect set in the brand profile |
| Accessibility | Keyboard navigation and screen-reader labels on all main screens |

**Suggested stack:** TypeScript end to end, Next.js for the app, Postgres for data, Redis with BullMQ for background jobs, Amazon SES for email, Paymob (Egypt) and Stripe (UAE company) for billing, and DataForSEO for search data and AI-visibility scans. Most projects worth studying use the same language.

## Plans and pricing

These prices are a proposal to test with your first customers, not market research. Set the limits only after measuring what one customer costs you in AI, SEO data and email each month.

|  | Starter | Growth | Agency |
| --- | --- | --- | --- |
| Proposed price (monthly) | $29 | $79 | $199 |
| For | One small business doing it themselves | A business that wants every channel | Agencies and freelancers |
| Businesses (workspaces) | 1 | 1 | 10, then extra per workspace |
| Weekly AI plan | Yes | Yes | Yes, per workspace |
| Site audit pages | 500 | 2,000 | 2,000 per workspace |
| Tracked keywords | 50 | 200 | 200 per workspace |
| AI-visibility questions | 20, scanned twice a month | 60, scanned twice a month | 60 per workspace |
| Blog articles per month | 4 | 12 | 12 per workspace |
| Social accounts | 3 | 10 | 10 per workspace |
| Email contacts / sends per month | 500 / 2,500 | 5,000 / 25,000 | Pooled 25,000 / 125,000 |
| Team members | 1 | 3 | Unlimited, plus client approvers |
| Reports | Weekly email | Weekly email + monthly report | White-label reports |

Yearly billing gets two months free. Every plan starts with a 14-day trial. WhatsApp messages, extra email sends and extra AI scans are sold as add-ons.

**Regional prices.** Charge Gulf customers the USD prices above (or the SAR and AED equivalents). Test Egyptian prices in EGP at roughly 40 to 50% of the USD price, because Egyptian small businesses spend far less on software. Treat both as hypotheses for phase 0 interviews. The cost estimate below shows what that does to margins.

## Running costs and AI providers

**Recommendation: Claude for writing and planning, DataForSEO for search and AI-visibility data, Amazon SES for email, and Paymob plus Stripe or Tap for payments.** One DataForSEO account covers SEO data and AI-visibility scans across ChatGPT, Gemini, Claude, Perplexity and Google AI Overviews, so you build one integration instead of five.

| Job | Recommended provider | Price | Why |
| --- | --- | --- | --- |
| Weekly plan (deciding what to do) | Claude Opus 5.5 | $4 input / $20 output per million tokens | Best reasoning; runs once a week per customer, so the total stays small |
| Articles, posts, newsletters, WhatsApp messages | Claude Sonnet 5.5 | $2 / $10 per million tokens | Strong writing at half the Opus price; test Arabic quality on 20 real samples before locking in |
| Short tasks (tagging, pulling mentions out of scan answers) | Claude Haiku 4.5 | $1 / $5 per million tokens | Cheapest; good enough for simple extraction |
| AI-visibility scans | DataForSEO AI Optimization API | From $0.001 per result row; LLM Mentions $0.10 per request + $0.001 per row | All AI engines in one API; its scraper shows what real users see, not just raw API answers |
| Rank tracking and SERPs | DataForSEO SERP API | $0.60 per 1,000 checks (standard queue) | Cheapest reliable source |
| Keyword data | DataForSEO Keywords Data | $0.06 per task of up to 1,000 keywords | Same account |
| Email | Amazon SES | $0.10 per 1,000 emails | Cheapest at scale |
| Payments in Egypt | Paymob | 2.75% + EGP 3 per payment | Cards, Meeza and mobile wallets; supports subscriptions |
| Payments in the Gulf | Stripe, through your UAE company | Check at signup | Stripe's support for Egypt-registered businesses is limited |

Run non-urgent AI jobs (weekly plans, scan analysis) through Claude's Batch API at 50% off, and cache the brand profile in prompts.

### Estimated monthly cost per customer

These are estimates from the list prices above and assumed usage at each plan's limits; measure real costs in the MVP before fixing prices. An AI-visibility answer is assumed to cost about $0.02 including web search; that is the number most worth checking.

| Cost item | Starter | Growth |
| --- | --- | --- |
| AI-visibility scans (questions × engines × 2 scans) | 20 × 3 × 2 = 120 answers, about $2.40 | 60 × 4 × 2 = 480 answers, about $9.60 |
| Rank tracking (weekly) | 200 checks, about $0.12 | 800 checks, about $0.48 |
| Site audit and keyword ideas | about $0.50 | about $1.50 |
| AI writing (plan, articles, posts, newsletter) | 4 articles, about $1.50 | 12 articles, about $4.00 |
| Email sending | 2,500 emails, $0.25 | 25,000 emails, $2.50 |
| Hosting, storage, monitoring share | about $1.00 | about $2.00 |
| **Total** | **about $6** | **about $20** |
| Margin at USD price | about 79% of $29 | about 75% of $79 |
| Margin at 45% Egyptian price | about 54% of \~$13 | about 44% of \~$36 |

Egyptian margins sit below the 70% target, so in Egypt either keep AI-visibility scans to once a month on Starter or price closer to 60% of USD. WhatsApp messages are not included: Meta charges per message and that cost passes to the customer.

## Release plan

Each phase must prove one thing before the next starts. Timing depends on your hours per week and is left for you to set.

| Phase | What ships | What it must prove before moving on |
| --- | --- | --- |
| 0. Validation | A landing page, a free "AI visibility check" that emails a one-page report, and 20 interviews with owners in your chosen niche | At least 100 free checks and 10 people willing to pay |
| MVP | Onboarding and brand profile, SEO audit, keyword ideas, rank tracking, AI visibility scans, weekly AI plan, blog drafts with WordPress publishing, social posts for 3 networks, dashboard, weekly results email, billing | 10 paying customers; 60% approve a plan each week |
| v1 | Email marketing, more social networks, post analytics, Google Search Console and Analytics, content gaps, local SEO, monthly reports, team roles, client approval links | 100 paying customers; churn under 5% a month |
| v2 | Automations, customer inbox, white label, AI assistant chat, AI crawler tracking, referral program | Driven by what paying customers ask for most |

## Risks, out of scope, and open questions

### Risks

| Risk | Why it matters | How to reduce it |
| --- | --- | --- |
| Crowded market | Many SEO, social and email tools exist, some very cheap | Win on one niche, the weekly plan, and plain language, not on features or price |
| AI and data costs eat the margin | Every scan, draft and keyword lookup costs money | Measure cost per workspace from day one; cache shared data; enforce plan limits |
| Social platform approvals | Meta, LinkedIn and TikTok review apps before allowing posting; X charges for API access | Apply early; launch with the networks already approved |
| Email abuse | One spammer can damage sending reputation for all customers | Double opt-in, complaint monitoring, automatic suspension, per-customer sending domains |
| AI drafts that are wrong | A false claim in a post hurts the customer's reputation | Customer approves everything; drafts use only brand-profile facts |
| Your own marketing inexperience | The product must teach marketing to customers you also have to reach | Interview customers in phase 0; the product's own blog and AI-visibility check become your marketing |
| License mistakes | Copying AGPL or GPL code could force you to publish your source | Study the open source projects, write your own code; copy only from MIT projects with their notice |

### Out of scope

Paid ads management, website building, a full CRM with deals and pipelines, phone or SMS marketing, and a mobile app (the web app must work well on phones instead).

### Open questions

| Question | Decision |
| --- | --- |
| Which market first? | Egypt, then Saudi Arabia and the UAE (your answer) |
| Product name | Rawwejly (روّجلي), your choice; register domains and file the trademark |
| Cost per customer | About $6 on Starter and $20 on Growth (see Running costs) |
| AI providers | Claude for writing and planning, DataForSEO for AI-visibility scans (see Running costs) |
| Arabic and right-to-left in the first version? | Yes (your answer) |
| Sell to businesses or agencies first? | Both from launch (your answer) |

Still open:

- [ ] Register rawwejly.com and rawwejly.ai, claim the social handles, and file the trademark in Egypt, Saudi Arabia and the UAE
- [ ] Choose where to register the UAE company (free zone or mainland), with an accountant

Also decided (your answers): Modern Standard Arabic is the default for drafts; a UAE company will be registered for Stripe and Gulf customers; all niches are served, with templates for clinics, restaurants, real estate and online shops.

## Sources

Prices checked on 2026-10-04: Claude model prices from Anthropic's API pricing; [DataForSEO SERP, keyword and backlinks pricing](https://dataforseo.com/pricing/backlinks/backlinks); [DataForSEO AI Optimization API](https://dataforseo.com/ai-optimization-api); [Amazon SES pricing](https://www.mailblast.io/blog/ses/amazon-ses-pricing); [Paymob pricing](https://paymob.com/en/pricing) and [subscriptions](https://paymob.com/en/subscriptions); [Stripe availability in Egypt](https://www.useaxra.com/payment-gateways/stripe/in/eg). The per-answer cost of AI-visibility scans is an estimate, not a quoted price.
