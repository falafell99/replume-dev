<p align="center">
  <img src="assets/banner.png" alt="Replume: Your coding agents get things done. We help you trust what they did." width="760">
</p>

<p align="center">
  <a href="https://replume.dev">replume.dev</a> ·
  <a href="https://x.com/rafaelibayev">@rafaelibayev</a> ·
  <a href="mailto:hello@replume.dev">hello@replume.dev</a>
</p>

# Replume

**A verification layer for AI coding agents.** Replume runs on top of Claude Code, Codex, Cursor and soon Hermes Agent. It watches each step the agent takes, steers it back when it leaves the task, and checks the finished commit against what you actually asked for. Then you know what the agent did without re-reading the whole diff.

Replume never writes code and never calls a model itself. It does not replace your agent; it checks it.

> **Status: private beta.** This repository holds the docs, the changelog and the issue tracker. The source code is not public yet. Join the beta at [replume.dev](https://replume.dev).

## Why

Agents say "done" when the task is not done. Tests can pass while the behaviour is wrong, and agents sometimes weaken the tests to get green. In [one 2026 study](https://arxiv.org/abs/2606.09863), when coding agents failed a task and stated an outcome, they reported success 75.8% of the time.

Review gets slower as agents produce more code. Replume moves the checking to where the work happens.

## How it works

1. **Set up once.** `replume init` creates `.replume/` in your repository and installs agent hooks where the agent supports them. It prints a matrix of what Replume can observe, block and steer for each agent.
2. **State the task.** `replume task "…"` writes a short task contract: what was asked, where the agent may work, and which scenarios must pass.
3. **The agent works; Replume watches.** Every step is recorded. Rules warn or block when the agent edits outside the task, deletes or weakens tests, runs destructive commands or loops. Replume tells the agent what to do instead.
4. **Verify the commit.** `replume verify` checks out that exact commit in an isolated working copy with a clean environment and runs your team's scenarios. If Docker is available, scenarios run in a container with no network.
5. **Read the report.** An example:

```
DONE         (2)  reset sends one email to the user · login still works
EXTRA        (1)  src/analytics.js: changed, not part of the task
NOT CHECKED  (1)  src/server.js: no scenario declares coverage
```

Exit codes: `0` pass, `1` fail, `2` incomplete, so `replume verify` can be a required check on commits and PRs.

## Principles

- **Honest by default.** A changed file that no scenario covers is reported as NOT CHECKED, never as passed. Nothing shows green unless it actually ran.
- **Neutral.** One layer for every agent, instead of each vendor checking its own agent.
- **The hard stop is at the commit.** Live hooks help, but they can be switched off on a developer machine. The commit check is the one you can enforce.

## Supported agents

| Agent | Watch | Block or steer live | Verify the commit |
|---|---|---|---|
| Claude Code | yes | yes | yes |
| Codex | yes | partial | yes |
| Cursor | beta | not yet | yes |
| Hermes Agent | in development | in development | yes |
| Any other agent | via git | no | yes |

`replume init` prints the exact matrix for your setup.

## Commands

| Command | What it does |
|---|---|
| `replume init` | Create `.replume/`, install hooks where supported, print the capability matrix |
| `replume task "<text>"` | Write the task contract for the next agent task |
| `replume watch` | Live view of the agent's steps and rule hits |
| `replume verify` | Run the contract and scenarios on a commit in an isolated copy |
| `replume report` | Re-print the saved report for a commit |

## Feedback

Questions, bugs and ideas are welcome in [Issues](../../issues). For beta access, write to [hello@replume.dev](mailto:hello@replume.dev).

---

Built by [Rafael Ibayev](https://x.com/rafaelibayev). Replume is not affiliated with Anthropic, OpenAI, Cursor or GitHub; their names are used only to say what Replume works with.
