---
name: iw-tanstack-starter
description: Load when the user asks to create, develop, debug, upgrade, or maintain a TanStack Start/TanStack starter React project, including phrases like "tanstack starter", "TanStack Start demo", "Start app", or "use the TanStack starter". Use for scaffolding, routing, styling, builds, and framework-specific maintenance. Do not load for unrelated React-only tasks unless TanStack Start or the TanStack CLI is involved.
---

# iw-tanstack-starter

Treat "tanstack starter" as TanStack Start unless the user clearly means a different TanStack template. Use current docs first; TanStack Start and the TanStack CLI move quickly.

## Fresh docs first

Use the host's documentation lookup workflow when available. In Codex environments that provide `find-docs`, load it and use Context7 through `ctx7`; in repositories with an `AGENTS.md` requiring `ctx7`, follow that local instruction.

Always resolve the library before fetching docs unless the user gave a Context7 ID:

```bash
npx ctx7@latest library "TanStack Start" "<full user request>"
npx ctx7@latest docs /websites/tanstack_start_framework_react "<full user request>"
```

For CLI scaffolding flags, query the CLI docs too:

```bash
npx ctx7@latest docs /tanstack/cli "TanStack CLI create Start React project flags"
```

For version-specific work, include the version in the `library` query and use the versioned ID from the results if one is listed, for example `/org/project/version`. If no versioned ID is listed, use the latest Context7 docs and explicitly say that a version-specific index was not available.

Read `references/practices.md` when creating a project from scratch, changing framework config, or diagnosing build/routing issues.

## Default creation flow

Prefer the official CLI over hand-written scaffolding for new apps:

```bash
npx @tanstack/cli@latest create <app-name> --framework React --deployment nitro --package-manager npm --no-examples --no-toolchain --no-git --no-intent --target-dir . --force --non-interactive --yes
```

Adjust flags to the user's package manager, target directory, deployment adapter, examples preference, and toolchain. If the user wants the most standard path and the directory can be overwritten, use the CLI directly.

After scaffolding, inspect the generated files before editing. Current standard locations are usually `src/routes/__root.tsx`, `src/routes/index.tsx`, `src/router.tsx`, `src/styles.css`, `vite.config.ts`, `tsr.config.json`, and generated `src/routeTree.gen.ts`.

## Development rules

- Keep root document responsibilities in `src/routes/__root.tsx`: metadata, stylesheet links, `HeadContent`, `Scripts`, and the app shell.
- Put page content in file routes under `src/routes`; use `createFileRoute('/')` for the homepage.
- Let route tree generation happen through `tsr generate` or the build/dev workflow; avoid manual edits to `src/routeTree.gen.ts`.
- Keep `tanstackStart()` before the React Vite plugin when hand-editing `vite.config.ts`.
- Preserve the generated Tailwind setup unless the user explicitly asks to remove it.
- For "no dark mode", search for `dark`, `prefers-color-scheme`, and `color-scheme`; remove toggles/media queries and keep a light base background and foreground.
- Verify with `npm run build`. If tests exist or the scaffold includes a test script, run it and fix template-level failures rather than reporting an avoidable broken script.

## Gotchas

- `@tanstack/create-start` may print deprecation guidance; prefer `npx @tanstack/cli@latest create`.
- If a test script reuses full `vite.config.ts` and starts framework plugins unnecessarily, create a small `vitest.config.ts` for unit/smoke tests.
- Do not cite stale hand-written commands when the user asks for "latest"; re-query Context7 first.
