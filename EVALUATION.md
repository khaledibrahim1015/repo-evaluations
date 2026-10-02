# Agent-Orchestration Landscape: Evaluation and Startup Idea Validation

_Snapshot taken 2026-10-02 from shallow clones of each repo's default branch._

## TL;DR

1. **Don't build another Multica-style board.** The space is crowded, Multica is moving fast (it ships most weekdays), and Multica's license forbids running a fork as a hosted service without a commercial license.
2. **You picked the right problem.** Hosting is hard, and getting agent work to run somewhere other than the user's laptop is the gap. Both leaders agree: Multica and Paperclip each have a **cloud runtime on a waitlist** right now.
3. **Don't compete on "we host it for you."** Multica, Paperclip, and the agent vendors themselves (cloud sessions from Anthropic, OpenAI, Cursor and GitHub) will all offer that. Generic sandboxes (E2B, Daytona, Modal, Cloudflare, `kubernetes-sigs/agent-sandbox`) are already interchangeable plugins.
4. **The opening is the part every project says it does *not* do:** keeping credentials and network access secure, and making sure the repo actually builds and tests inside the sandbox. Ship it as a runtime that runs **in the customer's own cloud account** and plugs into *any* board (Multica, Paperclip, Mesa).
5. **It fits your background.** Your own repos `provisionStack`, `instance-type-availability-module` and `Kube-Operator` are exactly this skill set.

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

## Appendix: sources read
- `multica-ai/multica`: `README.md`, `VISION.md`, `CLI_AND_DAEMON.md`, `LICENSE`, `apps/docs/content/docs/security-model.mdx`, `server/internal/cloudruntime/`, `server/internal/handler/cloud_runtime.go`, landing-page i18n strings ("Cloud runtime (waitlist)").
- `paperclipai/paperclip`: `README.md`, `ROADMAP.md`, `LICENSE`, `packages/plugins/sandbox-providers/{SANDBOX-REQUIREMENTS.md, e2b, kubernetes}`, `packages/paperclip-runner/README.md`.
- `msoedov/mesa`: `README.md` (including its landscape comparison table), `LICENSE`, `internal/` layout.
- `Claw-Company/clawcompany`: `README.md`, `LICENSE`, repo layout.
- `Bhargs24/keel`: `README.md` (including the FAQ), `LICENSE`.
