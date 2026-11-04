# dependabot-agent-fixture-removal

> **Temporarily reshaped (November 2026).** This repo is a deliberately vulnerable npm workspace while the `targeted` strategy of dependabot-agent is proven live. The commit that added this note gets reverted afterwards, restoring the setup described below.

**Test fixture. Not for use.**

Exercises [dependabot-agent](https://github.com/michaelpipkin/dependabot-agent)'s handling of an override for a package that was **never the subject of a Dependabot alert** — a hand-added pin the agent must leave untouched.

## The setup

- `debug@2.6.9` is installed. It declares `ms@2.0.0`.
- An override forces `ms` to `>=2.1.0 <3`. It resolves to `ms@2.1.3`.
- There are no vulnerabilities and no `ms` alert on this repo — the `ms` override is a hand-written pin, not something the agent authored from an alert.

## What the agent does (0.1.13+)

Since 0.1.13 the agent only reconciles overrides for packages that were **alerted at some point** (it fetches all alert states to tell a resolved alert from a never-existent one). `ms` was never alerted here, so the agent leaves the override alone — honoring the guarantee that overrides not tied to any alert are never removed automatically:

```
📡 Fetching Dependabot alerts from GitHub...
   Found 0 open npm Dependabot alert(s) (0 across all states).
✅ No override changes needed.
```

> **History:** before 0.1.13 the agent *did* remove this override (reading `debug@latest`'s safe `ms` range and judging the pin redundant). That was the bug this repo now guards against — removing a pin the agent never authored. The removal decision itself is proven by the unit tests in the main repo; a genuine live removal requires a package with real alert history whose dependents have since moved on, which a constructed fixture can't fabricate.

## Run it

```bash
npm install
dependabot-agent --repo michaelpipkin/dependabot-agent-fixture-removal --dry-run
```

(The repo needs Dependabot alerts *enabled* so the fetch returns an empty list rather than a 403; it does.)
