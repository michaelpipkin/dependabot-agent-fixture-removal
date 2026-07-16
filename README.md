# dependabot-agent-fixture-removal

**Test fixture. Not for use.**

Exercises [dependabot-agent](https://github.com/michaelpipkin/dependabot-agent)'s **override removal** — the case where an override is no longer needed because upstream has moved on, so the agent ages it out.

## The setup

- `debug@2.6.9` is installed. It declares `ms@2.0.0`.
- An override forces `ms` to `>=2.1.0 <3` (as if it were once load-bearing). It resolves to `ms@2.1.3`.
- **`debug@latest` declares `ms ^2.1.3`** — above the override floor. Upstream moved on.

There are no vulnerabilities and no Dependabot alerts here; the `ms` override is an **orphan** (no open alert). The agent's orphan-reconciliation path queries the npm registry, sees that `debug@latest` now requests a safe `ms` range, and removes the override:

```
✂️  Override for ms (>=2.1.0 <3) is no longer needed — latest upstream versions all request safe ranges: ^2.1.3
   - REMOVE ms: ">=2.1.0 <3"
```

Crucially it reads `debug@latest`'s range, not the installed `debug@2.6.9`'s (`2.0.0`) — the forward-looking check the removal logic is built on.

## Run it

```bash
npm install
dependabot-agent --repo michaelpipkin/dependabot-agent-fixture-removal --dry-run
```

(The `--repo` is only used to fetch alerts — there are none — so any repo works; the removal decision is registry-driven, not alert-driven.)
