# Agent-Orchestration Landscape: Evaluation and Startup Idea Validation

_Snapshot taken 2026-10-02 from shallow clones of each repo's default branch._

## TL;DR

1. **Don't build another Multica-style board.** The space is crowded, Multica is moving fast (it ships most weekdays), and Multica's license forbids running a fork as a hosted service without a commercial license.
2. **You picked the right problem.** Hosting is hard, and getting agent work to run somewhere other than the user's laptop is the gap. Both leaders agree: Multica and Paperclip each have a **cloud runtime on a waitlist** right now.
3. **Don't compete on "we host it for you."** Multica, Paperclip, and the agent vendors themselves (cloud sessions from Anthropic, OpenAI, Cursor and GitHub) will all offer that. Generic sandboxes (E2B, Daytona, Modal, Cloudflare, `kubernetes-sigs/agent-sandbox`) are already interchangeable plugins.
4. **The opening is the part every project says it does *not* do:** keeping credentials and network access secure, and making sure the repo actually builds and tests inside the sandbox. Ship it as a runtime that runs **in the customer's own cloud account** and plugs into *any* board (Multica, Paperclip, Mesa).
5. **It fits your background.** Your own repos `provisionStack`, `instance-type-availability-module` and `Kube-Operator` are exactly this skill set.
6. **Rebranding today's Multica for MENA isn't allowed** without both a commercial license and a separate branding waiver. The **last plain Apache 2.0 release, v0.1.19 (2026-04-08), can legally be forked and rebranded**, but it is an early web-only version. See section 8.
7. **Multica, Mesa and Paperclip are agent orchestrators, and they do stop at the pull request.** That said, GitLab, GitHub, Atlassian, Harness and Factory already sell agents across the whole SDLC. The opening is a neutral **SDLC control layer for agent-written code** (verification gates plus audit evidence), not another SDLC platform. See section 9.

---

## 1. The five repos at a glance

| | **Multica** | **Paperclip** | **Mesa** | **ClawCompany** | **Keel** |
|---|---|---|---|---|---|
| One-liner | Issue board where coding agents are teammates | Control plane to run a company of agents | Single-binary "zero-human company" board | Chat-style AI company with 38 preset roles | Idea → spec → build pipeline that runs inside your coding agent |
| Category | Human + agent **team workspace** | Agent **org / governance OS** | Agent org (lightweight) | Multi-agent **assistant** (not code-centric) | **Methodology / workflow kit** (not a platform) |
| Stack | Go + Postgres, Next.js, Electron, Expo | TypeScript + Postgres, React, Rust runner | Go + embedded SQLite + HTMX | TypeScript, Node | Python + Markdown prompts |
| Approx. source lines\* | ~613k | ~1.28M | ~12k | ~10k | ~7k |
| Where agents run | **User's machine** (local daemon over WebSocket) | Local, **or sandbox plugins**: E2B, Daytona, Modal, Cloudflare, Novita, Kubernetes; Claude Managed and AWS AgentCore drivers in the runner | Local (git worktree per run) | Local Node process | Inside the user's coding agent |
| Hosted offering | Cloud SaaS + **Cloud Runtime waitlist** | **Paperclip Cloud waitlist** | None | Closed-source sibling app (Sagit) | None |
| License | **Apache-2.0 + restrictions**: no hosted service or commercial embedding without a commercial license; branding must be kept | MIT | MIT | MIT | MIT |
| Activity | Very high (releases most weekdays) | Very high | Active (last commit 2026-10-01) | **Slowing** (last commit 2026-04-25; README says focus moved to Sagit) | Moderate (last commit 2026-09-03) |

\* Non-test `.go/.ts/.tsx/.py/.js/.rs` lines from `git ls-files`. These are rough sizes, not quality scores.

### Your claims, checked

| Your claim | Verdict | Evidence |
|---|---|---|
| "Paperclip is more complicated to turn into a product" | **True** | About 2× Multica's code. It covers org charts, budgets, governance, plugin system, eval kernel, a Rust runner, and eight sandbox providers. It targets "autonomous companies", which is a vaguer buyer than "an engineering team". |
| "Multica is simpler than Paperclip" | **True in concept, false in effort** | The idea is simple: board + assignee + daemon. The product is not: ~613k lines across web, desktop and mobile apps, 26 supported agent CLIs, Slack, Lark and other chat integrations, roles, and Helm. You can't catch up by cloning it, and its license blocks hosting a fork. |
| "The biggest challenge is hosting and making work run off the user's machine" | **True, and validated by the incumbents** | Multica's own security doc says runs are **unsandboxed with the daemon user's full permissions**, and its code has a `cloudruntime` client plus a "Cloud runtime (waitlist)" UI. Paperclip's sandbox contract states it **does not enforce network policy** and **cannot verify provider isolation**. Both are racing to close this gap. |

---

## 2. Each project in more detail

### Multica: the benchmark you want to emulate
- **Strengths:** a clean mental model (assign an issue to an agent like a colleague), real polish, a review gate before anything ships, and run replay with cost tracking. It supports 26 agent CLIs, so it isn't tied to one vendor.
- **Weaknesses / gaps:**
  - **Security is pushed onto the user.** `security-model.mdx`: "By default a run executes with the full permissions of the operating-system user running the daemon … Multica does not sandbox the filesystem." Claude Code runs with `bypassPermissions`; Codex runs with `danger-full-access`.
  - **Compute is pushed onto the user.** You must keep a machine on, logged in to each agent CLI.
  - Its cloud runtime is **vendor-hosted and on a waitlist**. That suits small teams, but enterprises that won't send source code to a third party are left out.
- **License impact:** you *can* use it internally, or ship only the backend/daemon/CLI with attribution. You *cannot* run a public instance of a fork, even for free, without a commercial license from Index Labs (Hong Kong) Limited.

### Paperclip: the "agent company OS"
- **Strengths:** the largest community (53k stars per Mesa's comparison table), MIT license, and a real **plugin SDK, including a sandbox-provider contract** (`packages/plugins/sandbox-providers/SANDBOX-REQUIREMENTS.md`). Budgets, governance and approvals are deep.
- **Weaknesses:** a heavy concept surface (CEO and org-chart metaphors) and a lot of operational weight (Node + Postgres + plugins). Its own docs admit sandbox isolation and outbound network policy are the **provider's** problem.
- **Why it matters for you:** it is the **easiest distribution channel**. A sandbox-provider plugin installs from npm by name in Paperclip's UI.

### Mesa: proof that the board is cheap
- A single Go binary on SQLite with HTMX, ~12k lines, plus an issue board, budgets, approvals, worktrees and a REST API for agents.
- **Lesson:** the board and orchestration layer can be built in about 12k lines. **The board is not the moat.** (You already forked it as `khaledibrahim1015/mesa`. It's a good base for experiments, since it's MIT.)

### ClawCompany: a different market
- A chat-style "company" of preset roles for research and reports. It isn't really competing for the coding-agent workspace.
- Maintenance is slowing, and its team moved to a closed macOS app (Sagit). **A signal:** open-source "AI company" projects struggle to monetize and pivot to closed desktop apps.

### Keel: a complement, not a competitor
- A methodology kit (specs, quality gates, `trespass` row-level-security prover) that runs inside a coding agent. No orchestration or hosting.
- **Lesson:** "what to build and proof it works" is a different layer. Keel's verification gates are the kind of thing a runtime could run automatically after every agent run.

---

## 3. The landscape by layer

```
 ┌──────────────────────────────────────────────────────────────────┐
 │ L4  Work surface / board   Multica · Paperclip · Mesa · Linear · │  CROWDED
 │                            Jira · GitHub Issues + Copilot agent  │
 ├──────────────────────────────────────────────────────────────────┤
 │ L3  Agent harness          Claude Code · Codex · Cursor · …      │  Vendors own it
 ├──────────────────────────────────────────────────────────────────┤
 │ L2  Run plane  ◄── GAP     credentials · egress policy · repo     │  Everyone says
 │                            env readiness · warm snapshots ·      │  "not our job"
 │                            audit · runs in the customer's cloud  │
 ├──────────────────────────────────────────────────────────────────┤
 │ L1  Sandbox compute        E2B · Daytona · Modal · Cloudflare ·  │  COMMODITY
 │                            k8s agent-sandbox · Firecracker · VMs │
 └──────────────────────────────────────────────────────────────────┘
```

- **L4 is crowded.** Multica and Paperclip are well funded or heavily starred and ship constantly. Mesa shows a small team can build the board in about 12k lines, so nobody wins on the board alone.
- **L3 belongs to the model vendors.** They also run their own cloud execution (for example Claude Code on the web, Codex cloud, Cursor background agents, and the GitHub Copilot coding agent). This is **platform risk** for anyone selling only "hosted agent runs".
- **L1 is a commodity.** Paperclip already treats these as interchangeable plugins.
- **L2 is the gap.** It is what stops a security or platform team from saying yes to agents running unattended against real repos and real credentials:
  1. **Credential brokering.** The agent never holds raw tokens. A proxy injects them per request, scoped per run, and they can be revoked. Today Multica runs inherit `~/.aws`, `gh` and SSH keys outright.
  2. **Outbound network policy.** An allowlist per workspace, with logging. Paperclip says it does not enforce this.
  3. **Environment readiness.** The "make the actual work run" problem: dependencies, service containers (Postgres, Redis), toolchains, warm snapshots so a run starts in seconds, and a **verified** check that the repo builds and its tests run before an agent is pointed at it.
  4. **Runs in the customer's own cloud account** (VPC, IAM, region, cost on their own bill), so source code never leaves.
  5. **Audit:** every command, network call and credential use, tied to the issue and run IDs that come from the board.

---

## 4. Recommended idea

### Working name: "Agent Runway"
**A self-hosted run plane for coding agents.** Install it in your own AWS/GCP/Azure account or Kubernetes cluster, and Multica, Paperclip or Mesa runs execute there in isolated sandboxes, with brokered credentials, outbound network allowlists, and repo environments that are verified to build and test.

**One-sentence pitch:** _"Your board decides what agents work on; Runway decides where they run, what they can touch, and proves the repo is ready, inside your own cloud."_

### Why this rather than a Multica clone

| | Build a Multica-like board | Build the run plane |
|---|---|---|
| Competition | Multica, Paperclip, Mesa, Linear/Jira, GitHub | Mainly the incumbents' *vendor-hosted* runtimes; no one owns runs in the customer's own cloud across all boards |
| Legal | Can't host a Multica fork | Integrates through public protocols and plugins (check the license; see risks) |
| Distribution | Start from zero | **Ride their communities**: a Paperclip plugin, a Multica runtime, Mesa's API |
| Buyer | A single developer (low willingness to pay) | Platform and security teams (budget, recurring contracts) |
| Fit with your skills | Front-end and product-heavy | Infra, Kubernetes operators, instance capacity: **your repos** |
| Effect of agent vendors' own clouds | Neutral | Positive: they are single-vendor and vendor-hosted, while you support every vendor and run in the customer's account |

### Architecture sketch

```
 Board (Multica / Paperclip / Mesa)  ──run request──►  Runway control plane (Go)
                                                         │  scheduling, quotas, audit, warm pool
                                                         ▼
                              Customer cloud account / k8s cluster (Kube-Operator)
                     ┌───────────────────────────────────────────────────────────┐
                     │ Sandbox (microVM or gVisor pod) per run                   │
                     │   agent CLI (claude / codex / …) + repo env snapshot      │
                     │        │ all traffic                                       │
                     │        ▼                                                   │
                     │ Egress + credential proxy: allowlist, token injection,     │
                     │ per-run scopes, full request log                           │
                     └───────────────────────────────────────────────────────────┘
```

Building blocks you can reuse instead of writing yourself: `kubernetes-sigs/agent-sandbox` or Firecracker/Kata for isolation, Cilium for network policy, devcontainer specs for environment definitions, and your own `instance-type-availability-module` for capacity and spot fallback.

### How each board would plug in
- **Paperclip:** implement the published sandbox-provider plugin contract. This is the fastest route to users.
- **Multica:** run the *unmodified* Multica daemon inside Runway sandboxes, operated by the customer for their own org (internal use is allowed). Keep its attribution notices. **Get the license reading confirmed by a lawyer before selling.**
- **Mesa:** it is MIT and has a REST API for agents, so integration is direct. It is also your reference implementation and test bed.

### Pricing hypothesis
Charge a platform fee per active sandbox-hour, or per seat with a usage band. Compute runs on the customer's own cloud bill, so you never carry inference or compute costs. Add an enterprise tier for SSO, audit export and on-prem support.

---

## 5. Risks, honestly

| Risk | Severity | Mitigation |
|---|---|---|
| Multica and Paperclip ship their own cloud runtimes and make them good enough | **High** | Lead with *runs in the customer's own cloud* and *works with every board*, which conflicts with their hosted-SaaS business model. Win the security-review buyer, not the indie developer. |
| Agent vendors bundle cloud execution, as Anthropic, OpenAI, Cursor and GitHub already do | High | Support every vendor, keep code in the customer's VPC, and give one audit trail across agents. Don't compete on running a single vendor's agent. |
| **Subscription auth.** Many users drive CLIs with consumer subscriptions, whose terms usually don't allow shared or hosted automation | Medium | Use API keys or enterprise credentials through the broker, never shared subscription logins. Check each vendor's terms. |
| Multica license interpretation | Medium | The customer operates the daemon in their own org, you never host it, and you keep attribution. Get a legal review first. |
| Generic sandbox vendors (E2B, Daytona) move up into this layer | Medium | Your edge is the board integrations, environment readiness and per-customer-cloud deployment, not the isolation itself. |
| Integration churn: Multica ships most weekdays | Medium | Use stable surfaces (daemon binary, plugin SDK, REST APIs) and run contract tests in CI against their latest releases. |

---

## 6. Validation plan, before building much

**Week 0–2: talk to people (no code)**
- Recruit 15–20 people from the Multica and Paperclip Discords and from platform teams. Ask:
  - Where do your agent runs execute today?
  - Did security block running them unattended? What did security ask for?
  - Would you put this in your own cloud, or are you fine with vendor hosting?
- **Continue if:** at least 5 teams say security, credentials or outbound access blocked or limited their rollout, **and** at least 3 agree to a paid pilot or a design-partner agreement.
- **Stop or pivot if:** most say "the vendor's cloud runtime is fine" or "a laptop is fine".

**Week 2–6: smallest useful product**
1. A Paperclip sandbox-provider plugin that targets a customer EKS cluster, built on `agent-sandbox` and Cilium.
2. An egress-and-credential proxy: a GitHub token and one cloud credential injected per run, with an allowlist and a request log.
3. An environment check: given a repo, build a devcontainer snapshot, prove `build` and `test` pass, and store it as a warm image.
4. A demo showing the same issue run on a laptop vs. in Runway, with the audit log and a blocked exfiltration attempt.

**Week 6–10:** run the Multica daemon inside Runway, take one design partner to production, and measure time-to-first-run, run success rate, and run cost.

---

## 7. A note on "launched by Claude / Anthropic"

This report helps you test the idea and shape it into something you can build and pitch. It can't speak for Anthropic, and nothing here suggests Anthropic would launch, fund or partner on it. If you build on Claude, the realistic route is the **Claude API / Agent SDK** (and, where relevant, Anthropic's startup programs), applying like any other founder.

---

## 8. Can you clone Multica, rebrand it, and target MENA?

### Today's Multica: no, not without two separate permissions
From Part I of the Multica License:
- **1(b) Branding.** You may not remove or change the Multica logo, name, or copyright information shown by any UI derived from Multica's UI code. This applies *even after that code is modified, moved, renamed or extracted*. A rebrand needs a written **branding waiver**.
- **1(a) Hosting and selling.** Offering it to other organizations as SaaS, as a managed service, or inside a product you sell or distribute needs a **commercial license**. A free public instance counts too.
- **1(d) Separate grants.** A commercial license does not include a branding waiver, and a branding waiver does not include a commercial license.
- **Allowed without either:** internal use within one organization. A MENA company can self-host stock Multica for its own teams.

### The legal route: fork the last Apache 2.0 release
Multica's license history (from `git log -- LICENSE`):

| | |
|---|---|
| Before 2026-04-01 | No LICENSE file. Only the release config declared `MIT`. **Don't rely on this.** |
| **2026-04-01 → 2026-04-08** | **Plain Apache 2.0** (`c52c6e69c` added it; `f4ba27f2f` replaced it with the restricted license) |
| Last release in that window | **`v0.1.19`**, commit `bd6731525e601695cd5dcd53036016bcff0a6956`, 2026-04-08 |
| Evidence it was published | Tagged releases v0.1.11–v0.1.19 in the window; the Homebrew formula declared `license: "Apache-2.0"` |
| What it contains | ~53k non-test lines (roughly 9% of today's size): Go backend + Postgres, Next.js web app, a daemon for Claude Code and Codex (plus OpenCode and OpenClaw), skills, multiple workspaces, real-time task lifecycle |
| What it lacks (all added later) | Desktop and mobile apps, most of today's 26 agent CLIs, squads, autopilots, chat-channel integrations, and the docs site |

Apache 2.0 grants are perpetual and irrevocable. Code released under it stays usable under Apache 2.0 after the project relicenses, which is how OpenTofu, OpenSearch and Valkey were forked. If you take this route:
1. **Fork from the `v0.1.19` tag** and nothing later.
2. **Remove the Multica name and logo everywhere.** About 159 files mention it, and Apache 2.0 grants no trademark rights. Choose your own name.
3. **Keep the `LICENSE` and copyright notices**, and mark the files you change (Apache 2.0 §4).
4. **Never copy or port anything from later Multica**, not even a one-line bug fix. Set a clean-room rule for everyone on the team.
5. **Have a lawyer confirm this before raising money or signing customers.** The key fact to confirm is that the repository and releases were public during that window.

**Trade-off:** you inherit an early version that is six months old, and you diverge from upstream on day one. It gives you a head start, not a finished product. Its architecture (Go + Postgres + Next.js + daemon) matches your Go background better than Paperclip does, and it is a fuller product than Mesa (MIT, ~12k lines).

### What would make a MENA version worth buying
Gaps in today's Multica, checked in its code:
- **No Arabic UI and no right-to-left layout.** UI locales are `en`, `fr`, `ja`, `ko` and `zh-Hans`; docs are translated to `fr`, `ja`, `ko` and `zh`.
- **No native WhatsApp or Microsoft Teams channel.** It has Slack, Lark, DingTalk, WeCom and Telegram.
- **The producer is a Hong Kong company** (Index Labs (Hong Kong) Limited).

Arabic and those two channels are features Multica could add in weeks, so **they are not a moat on their own**. The edges a local company can actually defend:
1. **In-country hosting for regulated buyers** (banks, telcos, government). The board, its data, and the agent runtimes stay inside the country, operated by a local entity. This is where the run plane from section 4 fits.
2. **Local procurement:** a local entity, local-currency invoices that meet local tax rules, Arabic contracts and support, and partnerships with system integrators.
3. **The channels people already use:** WhatsApp and Teams.

**Big caveat, model inference:** even with the board and runtimes in-country, Claude Code and Codex send code to the model provider. For regulated buyers, find out early what their regulators accept: in-region model hosting, approved providers, or open-weight models.

### Recommended path
| Option | What it is | Upside | Downside |
|---|---|---|---|
| **A. Partner** | Ask Multica for a MENA commercial license + branding waiver: https://www.multica.ai/contact-sales | You get every upstream update; it's the fastest and least risky | They may refuse or take a large share |
| **B. Services** | Install and support stock Multica inside MENA customers' own infrastructure, for their internal use | Revenue from day one; you learn what buyers need | A services margin, not a product. Confirm in writing whether running it *for* a customer counts as a "managed service" |
| **C. Your own product** | Fork `v0.1.19` (or Mesa), then make it Arabic-first and in-country, add WhatsApp/Teams, and add the run plane | Most upside; you own the brand | Most work; you compete with Multica directly |

**Suggested order:** in weeks 0–4, interview 15 engineering leaders at MENA banks, telcos, gov-tech and scale-ups, and ask Multica about option A at the same time. Ask the leaders:
- Do your teams use coding agents today?
- Is in-country hosting a hard requirement, and for which data (code, issues, run logs)?
- Does sending code to a model API pass your security review?
- Is an Arabic UI required, or just nice to have?

**Go to C if** Multica says no or the terms are bad, **and** at least 5 regulated organizations say residency is a blocker, **and** at least 3 agree to a paid pilot.

---

## 9. Are these just coding-agent orchestrators, and do they miss the SDLC?

### What each project really is
| Project | What it is | Where its "done" sits |
|---|---|---|
| **Multica** | Coding-agent orchestrator plus a team issue board | The issue moves to Done when its linked pull request merges |
| **Mesa** | Coding-agent orchestrator ("zero-human company") | A work block is marked `shipped` after human sign-off |
| **Paperclip** | General agent orchestrator ("company OS"); coding is one use case | Its review and approval stages are complete |
| **ClawCompany** | Multi-agent assistant for research and reports; not coding-focused | A report is delivered |
| **Keel** | Not an orchestrator: an SDLC method kit that runs inside one coding agent | Its own gates pass, then `/ship` |

So yes: Multica, Mesa and Paperclip decide *who works on what* and *run the agent*. Their job ends around the pull request.

### SDLC coverage, checked in each codebase
| Stage | Multica | Paperclip | Mesa | Keel |
|---|---|---|---|---|
| Requirements / spec | Issues and projects | Goals; planning mode with plan approvals | Strategic goals ("Apex blocks") with target metrics | ✅ Full spec documents, including every screen state |
| Design / architecture | — | Plans only | — | ✅ Design system, architecture, data model |
| Build | ✅ | ✅ | ✅ | ✅ (drives your agent) |
| Review | Human review status | ✅ Review and approval stages; the agent that did the work is excluded from reviewing it | Review chain following reporting lines | Code-reviewer agent |
| Test / quality gate | Shows CI status on the pull request card, read-only | Agents can publish review results as GitHub checks (experimental chat connector) | — | ✅ Gates that fail the build (placeholders, dependency boundaries, code ownership) |
| Security gate | — | — | — | ✅ `trespass` proves Postgres row-level security |
| CI/CD | Watches CI; an autopilot can trigger on a CI event | — | — | Planned in documents only |
| Release / deploy | Example plugin only (`release-checklist`) | Preview URLs for dev servers | An "approve for deployment" status, with no deploy integration | `/ship` command |
| Operate / incidents | Example plugin only (`deploy-sentinel`) | — | — | — |
| Traceability (requirement → test → release) | — | Goal ancestry on tasks | Alignment score | Inside its own documents |

**For the orchestrators, your instinct is right.** They cover planning (lightly), build, review and merge. At most they watch CI, and nothing after the merge is in their core product. **Keel is the mirror image.** It covers the SDLC thinking, but for one person and one agent, with no team, no runtime and no CI/CD.

### The market isn't empty: the big platforms are already there
Agents across the whole lifecycle are where the largest DevOps vendors are putting their effort:
- **GitLab Duo Agent Platform:** generally available in GitLab 18.8 (January 2026). Agents cover planning, coding, testing, deploying and monitoring. Available on GitLab.com and Self-Managed for Premium and Ultimate.
- **GitHub Copilot coding agent:** runs on GitHub Actions. You assign an issue; it plans, opens a pull request, runs the tests and asks for review. Agent HQ lets developers assign Codex, Claude or Copilot to issues and pull requests.
- **Atlassian Rovo Dev:** Jira plus Bitbucket, "from planning to deployment". It reviews code against acceptance criteria, and agentic steps for Bitbucket Pipelines were announced for Q1 2026.
- **Harness:** positions itself as the platform for the "autonomous SDLC", with agents running inside pipelines. Its *Agent DLC* (July 2026) solves a different problem: shipping AI agents as products.
- **Factory:** a "Software Factory" covering signal triage, code, validation, release and monitoring.

**A startup can't out-platform GitLab, GitHub and Atlassian.** "An AI SDLC platform" is too big and too contested.

### Where the real gap is
Each of those platforms runs the SDLC **inside its own walls, with its own agent first**. Three things nobody does in a vendor-neutral way:
1. **Proof between "the agent says it's done" and "this can merge":**
   - acceptance criteria mapped to tests;
   - tests actually run in a real environment;
   - security checks;
   - a human approver who is not the author.
2. **Change-management evidence for agent-written code:** who asked, which agent ran, what it executed, which credentials and network calls it used, which tests passed, who approved, and when it was deployed. Auditors expect this kind of evidence for change management, for example under SOC 2's change-management criterion (CC8.1). Security vendors are writing about it, but the products are early.
3. **Mixed tool estates:** many companies run Jira with GitHub or GitLab, Jenkins or Azure DevOps, and several agent CLIs. A single-vendor agent only covers its own slice.

### The refined idea: an SDLC control layer for agent-written code
Instead of "an SDLC platform":

```
 Any tracker (Jira, Multica, GitHub Issues)
        │  task + acceptance criteria
        ▼
 Controlled runtime (section 4) ──► agent does the work (Claude Code, Codex, …)
        │
        ▼
 Gates: acceptance criteria → tests → security → human approver ≠ author
        │                                  (reported as a required status check)
        ▼
 The customer's existing CI/CD (Actions, GitLab CI, Jenkins) ──► deploy
        │
        ▼
 An evidence pack per change, ready for auditors and regulators
```

- **Don't build a board or a CI system.** Plug into the ones customers already use, and report results as a required status check.
- **It's the section 4 run plane seen from the other side.** The run plane decides **where and how** agents run; the gates decide **what must be true before their work moves on**. Together they make one product.
- **Best first buyers are regulated teams** (banks, fintech, telcos). That lines up with the MENA angle in section 8.

**Risk:** GitHub and GitLab already have branch rules, required checks and audit logs, and could add evidence packs for agent changes. The edge has to come from being neutral (any tracker, git host, CI and agent), agent-specific, and ready for auditors.

### Questions to validate it with compliance and engineering leaders
- How do you prove to auditors today that an agent-written change was reviewed and tested? Who signs off?
- Which tracker, git host, CI and deploy tools do you use? One vendor or several?
- Has an agent-written change ever caused an incident or an audit finding?
- Which would you pay for first: the gates, the evidence pack, or the in-country runtime?

---

## Appendix: sources read
- `multica-ai/multica`: `README.md`, `VISION.md`, `CLI_AND_DAEMON.md`, `LICENSE`, `NOTICE`, `apps/docs/content/docs/security-model.mdx`, `server/internal/cloudruntime/`, `server/internal/handler/cloud_runtime.go`, `server/internal/integrations/`, `packages/views/locales/`, landing-page i18n strings ("Cloud runtime (waitlist)").
- `multica-ai/multica` history: `git log -- LICENSE NOTICE` (commits `c52c6e69c`, `f4ba27f2f`, `4f8969ef5`, `0314df3b8`, `00e206097`, `10746ad3a`, `9c69661f7`), release tags around April 2026, and the `v0.1.19` tree (`LICENSE`, `README.md`, `.goreleaser.yml`, `server/internal/`).
- `paperclipai/paperclip`: `README.md`, `ROADMAP.md`, `LICENSE`, `packages/plugins/sandbox-providers/{SANDBOX-REQUIREMENTS.md, e2b, kubernetes}`, `packages/paperclip-runner/README.md`.
- `msoedov/mesa`: `README.md` (including its landscape comparison table), `LICENSE`, `internal/` layout.
- `Claw-Company/clawcompany`: `README.md`, `LICENSE`, repo layout.
- `Bhargs24/keel`: `README.md` (including the FAQ), `LICENSE`, `template/.claude/commands/`.
- SDLC checks in code: Multica `apps/docs/content/docs/github-integration.mdx`, `server/internal/handler/github.go`, `server/internal/handler/autopilot_webhook.go`, `examples/plugins/`; Paperclip `docs/guides/execution-policy.md`, `server/src/services/chat-github-checks.ts`; Mesa `internal/models/models.go`, `internal/db/migrations/022_webhook_events.sql`.

**Web sources for section 9 (searched 2026-10-04):**
- [GitLab announces general availability of GitLab Duo Agent Platform](https://ir.gitlab.com/news/news-details/2026/GitLab-Announces-the-General-Availability-of-GitLab-Duo-Agent-Platform/default.aspx)
- [How GitLab Duo Agent Platform brings AI agents across the entire SDLC (SoftwarePlaza)](https://softwareplaza.com/it-magazine/how-gitlab-duo-agent-platform-brings-ai-agents-across-the-entire-sdlc/)
- [About GitHub Copilot cloud agent (GitHub Docs)](https://docs.github.com/copilot/concepts/agents/coding-agent/about-coding-agent)
- [Assigning and completing issues with coding agent in GitHub Copilot (GitHub Blog)](https://github.blog/ai-and-ml/github-copilot/assigning-and-completing-issues-with-coding-agent-in-github-copilot/)
- [OpenAI Codex (AI agent), Agent HQ section (Wikipedia)](https://en.wikipedia.org/wiki/OpenAI_Codex_(AI_agent))
- [Reimagining software delivery with AI-powered workflows in Jira & Bitbucket (Atlassian)](https://www.atlassian.com/blog/bitbucket/ai-powered-workflows-rovodev)
- [Best AI-native SDLC platforms to consider in 2026 (Codewave)](https://codewave.com/feeds/blog/best-ai-native-sdlc-platform-2026)
- [Harness launches Agent DLC (SiliconANGLE, 2026-07-21)](https://siliconangle.com/2026/07/21/harness-launches-agent-dlc-developers-deploy-ai-agents-using-familiar-processes-tools/)
- [Factory AI review 2026 (The AI Agent Index)](https://theaiagentindex.com/agents/factory-ai)
- [Can AI agents satisfy SOC 2 code review requirements? (Workstreet)](https://www.workstreet.com/blog/can-ai-agents-satisfy-soc-2-code-review-requirements)
- [How AI agents impact SOC 2 Trust Services Criteria (Teleport)](https://goteleport.com/blog/ai-agents-soc-2/)
