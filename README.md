# CLAUDE.md patterns that actually bind

Install: `npx skills add soul-sol/claude-md-patterns`

Your `CLAUDE.md` probably says *"write clean code, be careful with destructive operations, ask if unsure."* The agent reads it, agrees with it, and then does whatever the last 200 lines of context suggested. The file isn't wrong — **it just isn't a rule.** Nothing in it can be checked, so nothing in it binds.

A rule that binds has four parts:

1. **Trigger** — the exact condition where it applies ("any browser launch", "before every commit", "when a worker exits")
2. **Check** — a command, string, or state that is either true or false. If a reviewer can't grep for it, the agent can't enforce it on itself
3. **Stop condition** — what to do when the check fails, named explicitly (`ABORT: …`), not implied
4. **Evidence** — what must be left behind so the rule's application is visible afterward


> These came out of running Claude Code and Codex workers in parallel every day.
> The incidents behind them — what the agent claimed, what actually happened, and the
> gate that catches it next time — are at
> [status.lifestep.io/incidents](https://status.lifestep.io/incidents/).
> The full set is [The CLAUDE.md Pattern Library](https://lifestep1.gumroad.com/l/claude-md-pattern-library?utm_source=github&utm_medium=readme&utm_campaign=claude-md-patterns-lib) ($9),
> or the [Complete Agent Ops Kit](https://lifestep1.gumroad.com/l/complete-agent-ops-kit?utm_source=github&utm_medium=readme&utm_campaign=claude-md-patterns) ($29) if you also want
> the task briefs, the 25 review prompts and the deploy gate that go around these rules;
> what is here stays free and MIT either way.

## Audit your own file first

Before adopting any pattern here, see which of your existing rules an agent cannot act on:

**[claudemd.lifestep.io](https://claudemd.lifestep.io)** — paste a `CLAUDE.md` or `AGENTS.md` and it flags
truncation risk, headings with no rule under them, instructions with no verification command, prohibitions
with no stated alternative, and rules that read like controls but enforce nothing.

It is a static checker, not a model: no upload, no API call, no LLM. The page runs the checks in your browser
and cites the line each finding came from. Checks it cannot evaluate report *not evaluated* rather than
*pass* — absence of a shape to inspect is not evidence that the file is fine.

## 5 free patterns

Each file gives the misbehavior it fixes, the exact text to paste, why it binds, variations (monorepo/solo/team), and the anti-pattern — the plausible version that fails.

| # | Pattern | Stops |
|---|---|---|
| 05 | [Autonomy clause](patterns/05_autonomy-clause.md) | workers that stall waiting for approval nobody will give |
| 10 | [Done is evidence](patterns/10_done-is-evidence.md) | "it's finished" reports that were never verified |
| 16 | [Browser profile quarantine](patterns/16_browser-profile-quarantine.md) | automation touching your real browser profile (this one came from losing ~2,000 bookmarks) |
| 19 | [Look before you destroy](patterns/19_destructive-ops-lookfirst.md) | overwrites and deletes on the wrong target |
| 21 | [Reassign, don't retry](patterns/21_reassign-not-retry.md) | burning three attempts on a worker that will fail again |

Adopt these five first. Add more only when you hit the specific failure they address — a rule adopted after the incident gets followed; a rule adopted preemptively becomes wallpaper.

## Test that a rule binds

Open a **fresh session** (new process, no history) and probe it:

```bash
# 1. Can it restate the rule unprompted?
claude -p "Without looking anything up: what must you do before launching a browser in this project, and what is the abort condition?"

# 2. Give it a task that would violate the rule if the rule were absent.
claude -p "Take a screenshot of my inbox for the report."
```

Vague answer to (1) → the rule is too long or too far down the file. (2) proceeds without the guard → the rule is not binding. Tighten trigger and stop condition, probe again. A rule you have never probed is a rule you are guessing about.

## The full library (30 patterns)

Same format as the five above — each pattern names the failure it stops:

| Pattern | Stops |
|---|---|
| Pipefail standard | a failing test reported as a pass, because `$?` came from the pipe's last command |
| Deploy success marker | reading a rollback's "health checks passed" as a successful deploy |
| Warning is not failure | pulling a working worker out of rotation over an informational log line |
| Stall doctrine | calling a finished job stalled because it didn't print the marker you expected |
| Polling-loop trap | watch loops that die on a timeout and take the run's only observer with them |
| Exit-code completion | "it's done" accepted without the command and code that say so |
| Context budget | a correct rule that loses to the last 200 lines of context |
| Precise process kills | a cleanup that kills more than the thing you meant |

Plus 22 more — global/project split, nested overrides, brief contract, small-diff threshold, file ownership, worktree mandate, log-tail check, review gate, identity-actions-human-only, secret existence checks, memory file discipline, session parking, progress report format, model tier policy, bulk-read delegation, model pinning and others — with a 43-page PDF adoption guide, the probe test, and a Korean guide.

**→ [$9 on Gumroad](https://lifestep1.gumroad.com/l/claude-md-pattern-library?utm_source=github&utm_medium=readme&utm_campaign=claude-md-patterns-lib)**

Rules decide what an agent may do. They don't tell you what to hand it, or how to judge the
diff that comes back. The [Complete Agent Ops Kit](https://lifestep1.gumroad.com/l/complete-agent-ops-kit?utm_source=github&utm_medium=readme&utm_campaign=claude-md-patterns)
($29) covers the whole loop — this library in full, plus task-brief and change-scope templates, 25
adversarial review prompts to run against a worker's diff before it lands, the deploy/rollback gate,
and *Solo, Like a Team* in English and Korean. The same files bought as separate products: $49.

## Related free tools

- [agent-watch](https://github.com/soul-sol/agent-watch) — RUNNING/DONE/FAILED/STALL detection for background AI agents
- [ai-code-review-prompts](https://github.com/soul-sol/ai-code-review-prompts) — adversarial review prompts for AI-written code
- [claude-code-orchestration-ko](https://github.com/soul-sol/claude-code-orchestration-ko) — Korean guide + full template set

## License

MIT for the 5 patterns in this repository.

<!-- xlink:start -->
## Related free tools

- [CLAUDE.md Auditor](https://claudemd.lifestep.io) - paste your rules file and see which rules an agent cannot reliably follow
- [XLSX Inspector](https://xlsx.lifestep.io) — check workbooks for macros, external links and hidden sheets
- [DNS and SPF Check](https://dnscheck.lifestep.io) — records, SPF, DMARC and TLS expiry
- [Email Validator](https://emailcheck.lifestep.io) — syntax, MX, disposable and role addresses
- [QR Code Generator](https://qrcode.lifestep.io) — free PNG and SVG API, no signup
- [agent-watch](https://github.com/soul-sol/agent-watch)
- [ai-code-review-prompts](https://github.com/soul-sol/ai-code-review-prompts)
- [claude-code-orchestration-ko](https://github.com/soul-sol/claude-code-orchestration-ko)
- [xlsx-inspector-api](https://github.com/soul-sol/xlsx-inspector-api)
- [domain-info-api](https://github.com/soul-sol/domain-info-api)
- [email-validator-api](https://github.com/soul-sol/email-validator-api)
- [qr-code-api](https://github.com/soul-sol/qr-code-api)
- [Agent Ops for VS Code](https://github.com/soul-sol/vscode-agent-ops) - review prompts and agent rules in the Command Palette (VSIX install)
- [Go Exec Format Doctor Action](https://github.com/soul-sol/go-exec-format-doctor) - CI gate for binary architecture mismatches

The paid guide collection is available at [lifestep1.gumroad.com](https://lifestep1.gumroad.com).
<!-- xlink:end -->
