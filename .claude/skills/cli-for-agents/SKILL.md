---
name: cli-for-agents
description: >-
  Designs or reviews CLIs so coding agents can run them reliably: non-interactive
  flags, layered --help with examples, stdin/pipelines, fast actionable errors,
  idempotency, dry-run, and predictable structure. Use when building a CLI,
  adding commands, writing --help, or when the user mentions agents, terminals,
  or automation-friendly CLIs. Triggers on "build a CLI", "add a command",
  "write --help", "agent-friendly CLI", "automation-friendly", "non-interactive".
---

# CLI for Agents

Design and review CLIs that coding agents can run reliably without human intervention.

## When to Use

- Building a new CLI tool or adding commands to an existing one
- Writing `--help` text or usage documentation
- Reviewing a CLI for agent/automation compatibility
- User mentions agents, terminals, pipelines, or non-interactive usage

## Modes

This skill operates in two modes:

1. **Design mode** — Apply the principles below when building or adding CLI commands
2. **Review mode** — Use the [Review Checklist](#review-checklist) to audit an existing CLI

---

## Design Principles

### 1. Non-interactive first

Every input must be expressible as a flag or flag value. Do not require arrow keys, menus, or timed prompts. If flags are missing, **then** fall back to interactive mode — not the other way around.

**Bad:** `mycli deploy` → `? Which environment? (use arrow keys)`
**Good:** `mycli deploy --env staging`

### 2. Discoverability without dumping context

Agents discover subcommands incrementally: `mycli`, then `mycli deploy --help`. Do not print the entire manual on every run. Let each subcommand own its documentation so unused commands stay out of context.

### 3. `--help` that works

Every subcommand has `--help`. Every `--help` includes **Examples** with real, copy-pasteable invocations. Examples do more than prose for pattern-matching.

```text
Options:
  --env     Target environment (staging, production)
  --tag     Image tag (default: latest)
  --force   Skip confirmation

Examples:
  mycli deploy --env staging
  mycli deploy --env production --tag v1.2.3
  mycli deploy --env staging --force
```

### 4. stdin, flags, and pipelines

- Accept stdin where it makes sense (e.g. `cat config.json | mycli config import --stdin`).
- Avoid odd positional ordering; never fall back to interactive prompts for missing values.
- Support chaining: `mycli deploy --env staging --tag $(mycli build --output tag-only)`.

### 5. Fail fast with actionable errors

On missing required flags: exit immediately with a clear message and a **correct example invocation**, not a hang.

```text
Error: No image tag specified.
  mycli deploy --env staging --tag <image-tag>
  Available tags: mycli build list --output tags
```

### 6. Idempotency

Agents retry often. The same successful command run twice must be safe (no-op or explicit "already done"), not duplicate side effects.

### 7. Destructive actions

- Add `--dry-run` (or equivalent) so agents can preview plans before committing.
- Offer `--yes` / `--force` to skip confirmations while keeping the safe default for humans.

### 8. Predictable structure

Use a consistent pattern everywhere, e.g. `resource` + `verb`: if `mycli service list` exists, `mycli deploy list` and `mycli config list` should follow the same shape.

### 9. Structured success output

On success, return machine-useful data: IDs, URLs, durations. Plain text is fine; avoid relying on decorative output alone.

```text
deployed v1.2.3 to staging
url: https://staging.myapp.com
deploy_id: dep_abc123
duration: 34s
```

---

## Anti-Patterns

| Don't | Do Instead |
|-------|------------|
| Interactive prompts as default input | Flags for all inputs; interactive as fallback only |
| Dump full manual on every invocation | Layered `--help` per subcommand |
| `--help` with only prose descriptions | Include copy-pasteable **Examples** in every `--help` |
| Hang or wait on missing required flags | Exit immediately with error + example invocation |
| Decorative-only success output | Return machine-useful data (IDs, URLs, durations) |
| Non-idempotent commands | Same command twice = safe (no-op or "already done") |
| Destructive actions without safety net | `--dry-run` to preview, `--yes`/`--force` to skip confirms |
| Inconsistent command structure | `resource verb` pattern throughout (`service list`, `config list`) |
| Require complex positional arg ordering | Named flags; accept stdin with `--stdin` |

---

## Review Checklist

When reviewing an existing CLI, verify each item:

- [ ] **Non-interactive path** — Every command works without human interaction via flags
- [ ] **Layered help** — `--help` on every subcommand, not a global manual dump
- [ ] **Examples in `--help`** — Real, copy-pasteable invocations in every help text
- [ ] **stdin/pipeline support** — Accepts piped input where appropriate
- [ ] **Actionable errors** — Missing flags produce error + correct example invocation
- [ ] **Idempotency** — Running the same successful command twice is safe
- [ ] **Dry-run support** — `--dry-run` or equivalent for destructive actions
- [ ] **Confirmation bypass** — `--yes` / `--force` flags for scripted usage
- [ ] **Consistent structure** — Commands follow a predictable `resource verb` pattern
- [ ] **Structured success output** — Returns machine-useful data (IDs, URLs, durations)