# Combined marketing product plan

Oct 4, 2026 · khaled

## The combined product

**An AI marketing team for small businesses that don't have one.** One login and one bill replace an SEO tool, a social media scheduler and a newsletter tool. Every week the app checks how the business shows up on Google and in AI answers, then hands the owner a short plan with the blog post, social posts and newsletter already drafted. The owner approves, and the app publishes and reports back.

This suits a founder without a marketing background, because the product itself carries the marketing know-how. Customers don't need it either: they approve drafts instead of learning three tools.

You write the product yourself. The 10 projects are your textbooks: each one shows how a feature is really built, including the edge cases a first version usually misses.

### What to learn from each project

| Feature you build | Study | Where to look in its code |
| --- | --- | --- |
| SEO audit, keyword research, rank tracking | [OpenSEO](https://github.com/every-app/open-seo) | `src/server/features/audit`, `keywords`, `rank-tracking`, and how it calls the DataForSEO API |
| "Does ChatGPT, Claude or Gemini recommend you?" | [Notra](https://github.com/usenotra/notra), then OpenSEO | Notra: `packages/geo-core`, `apps/api/src/routes/geo-scans.ts`, `geo-visibility.ts`. OpenSEO: `src/server/features/ai-search` |
| Content gaps turned into blog drafts | Notra | `apps/dashboard/src/lib/geo/content-gaps-refresh.ts`, `packages/content-generation` |
| Findings written in plain language | [RankMeFast](https://github.com/SelmiAbderrahim/rankme.fast) | `server/src`: each finding names the page, why it matters and the fix; also its fake data providers for a free demo mode |
| Publishing to each social network | [Postiz](https://github.com/gitroomhq/postiz-app) | `libraries/nestjs-libraries/src/integrations/social/`: one file per network (login, posting, token refresh) |
| Scheduled posts, retries, failures | Postiz and [TryPost](https://github.com/trypostit/trypost) | Postiz: `apps/orchestrator/src/workflows/post-workflows`. TryPost: `app/Services/Social`, `app/Exceptions/Social` (error types per network) |
| AI writing in the customer's brand voice | TryPost | `app/Services/Brand`, `app/Services/Ai`, `app/Ai` |
| Sending email, bounces, spam complaints, unsubscribes | [Keila](https://github.com/pentacent/keila) | `lib/keila/mailings`: `delivery_worker.ex`, `rate_limiter.ex`, `message_actions/`, `sender_adapters/` (Amazon SES) |
| Email open and click tracking | Keila | `lib/keila/tracking` |
| Customer segments and automated journeys | [Dittofeed](https://github.com/dittofeed/dittofeed) | `packages/backend-lib/src/segments`, `journeys` |
| Forms, lead scoring, campaigns | [Mautic](https://github.com/mautic/mautic) | `app/bundles/FormBundle`, `PointBundle`, `CampaignBundle`, `LeadBundle` |
| Shared customer inbox | [erxes](https://github.com/erxes/erxes) | `backend/plugins/frontline_api` |
| Accounts, plans and usage billing | OpenSEO | `src/server/billing`, `src/server/auth` |

Mixpost is the only one not worth studying: it has had no commits since March 2026 and Postiz covers the same ground.

### Learning without copying

- Reading their code to understand an approach, then writing your own, is fine for every one of these licenses. Licenses protect the code text, not the idea.
- Do not paste code from the AGPL or GPL projects (Postiz, TryPost, Keila, Notra, RankMeFast, erxes, Mautic). Doing so could oblige you to publish your whole product's source. Take notes in your own words, close the file, then write.
- The MIT projects (OpenSEO, Dittofeed, Mixpost) do allow copying snippets, as long as you keep their copyright notice.
- Their names and logos are trademarks, so your product needs its own.

### Your own stack

Use TypeScript end to end, with Next.js, Postgres and a job queue such as BullMQ, unless you already know another stack well. Most of the projects worth studying (OpenSEO, Notra, Postiz, Dittofeed) are TypeScript, so what you read carries straight into what you write.

### Build order

1. **Get found + weekly AI plan.** Study OpenSEO, Notra and RankMeFast. This is sellable alone and tests whether people pay.
2. **Social publishing.** Start with 3 networks, studying Postiz's provider files. Apply for Meta, LinkedIn and TikTok app approval early, because it takes time.
3. **Email newsletters.** Study Keila and send through Amazon SES.
4. **Automations, then a customer inbox,** once paying customers ask. Study Dittofeed and Mautic, then erxes.

## The 10 projects at a glance

Activity is commits and distinct authors on the default branch from 2026-07-04 to 2026-10-04, counted from a clone of each repo. Self-hosting effort is the number of services in the project's own Docker Compose file.

| Project | What it does | License (matters only if you copy code) | Activity (90 days) | Stack | Self-hosting |
| --- | --- | --- | --- | --- | --- |
| [OpenSEO](https://github.com/every-app/open-seo) | SEO suite: keywords, rank tracking, audits, backlinks, AI visibility | MIT. Free to sell, changes can stay private | 358 commits, 15 authors | TypeScript, TanStack Start, Cloudflare Workers, Postgres or SQLite | 1 container + DataForSEO key |
| [TryPost](https://github.com/trypostit/trypost) | Social scheduling for 12 networks, AI copilot, agency workspaces | AGPL-3.0. Sell it, but publish your changes | 237 commits, 10 authors | PHP, Laravel 13, Inertia, Postgres, Redis | 4 services |
| [Postiz](https://github.com/gitroomhq/postiz-app) | Social scheduling, analytics, API for n8n and Zapier | AGPL-3.0. Sell it, but publish your changes | 522 commits, 6 authors | Node.js, NestJS, Next.js, Postgres, Redis, Temporal | 9 services |
| [Mautic](https://github.com/mautic/mautic) | Full marketing automation: email, campaigns, segments, forms, lead scoring | GPL-3.0. Hosting it is allowed; "Mautic" name is trademarked | 2,274 commits, 56 authors | PHP, Symfony, MySQL | Single-tenant: one install per customer |
| [Notra](https://github.com/usenotra/notra) | AI-search (GEO) visibility tracking and content drafts | AGPL-3.0. Sell it, but publish your changes | 883 commits, 14 authors | Bun, Next.js, Hono, Postgres | No Compose file; built for their own cloud |
| [RankMeFast](https://github.com/SelmiAbderrahim/rankme.fast) | SEO audits, rank tracking, AI visibility in plain language | AGPL-3.0. Sell it, but publish your changes | 33 commits, 1 author | Node.js, MongoDB, Postgres, Redis | 6 services |
| [Dittofeed](https://github.com/dittofeed/dittofeed) | Customer journeys across email, SMS, push, WhatsApp | MIT, but multi-tenancy and white-labeling are closed source | 3 commits, 1 author | TypeScript, Postgres, ClickHouse, Kafka, Temporal | 17 services |
| [Keila](https://github.com/pentacent/keila) | Newsletters and sign-up forms (Mailchimp alternative) | AGPL-3.0; its cloud and billing code is "all rights reserved" | 34 commits, 3 authors | Elixir, Phoenix, Postgres | 2 services |
| [erxes](https://github.com/erxes/erxes) | Inbox, CRM, tickets, sales plugins | AGPL plus a clause banning SaaS that competes with erxes Inc. | 757 commits, 31 authors | Node.js, Nx microservices, MongoDB | Heavy microservices |
| [Mixpost](https://github.com/inovector/mixpost) | Social scheduling (Lite edition) | MIT, but Pro features are paid and closed | 0 commits; last commit 2026-03-16 | PHP Laravel package | Needs a host Laravel app |

## Sources

License files, READMEs, Compose files and commit history were read from each repository's default branch on 2026-10-04: [OpenSEO](https://github.com/every-app/open-seo), [TryPost](https://github.com/trypostit/trypost), [Postiz](https://github.com/gitroomhq/postiz-app), [Mautic](https://github.com/mautic/mautic), [Notra](https://github.com/usenotra/notra), [RankMeFast](https://github.com/SelmiAbderrahim/rankme.fast), [Dittofeed](https://github.com/dittofeed/dittofeed), [Keila](https://github.com/pentacent/keila), [erxes](https://github.com/erxes/erxes), [Mixpost](https://github.com/inovector/mixpost). The openalternative.co pages could not be opened from this environment, so Notra was matched to usenotra/notra (opencoredev/notra is an older copy of the same project). Star counts were not available and are left out. This is not legal advice; have a lawyer confirm the license reading before launch.
