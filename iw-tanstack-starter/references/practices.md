# TanStack Start Practices

Use this reference for project creation, maintenance, and troubleshooting after loading `iw-tanstack-starter`.

## Documentation Sources Observed

Use these as known-good starting points, but re-query before relying on exact APIs or flags:

- Context7 library ID: `/websites/tanstack_start_framework_react`
- Context7 CLI library ID: `/tanstack/cli`
- Current official docs path: `https://tanstack.com/start/latest/docs/framework/react/`
- Getting started: `https://tanstack.com/start/latest/docs/framework/react/getting-started.md`
- Build from scratch: `https://tanstack.com/start/latest/docs/framework/react/build-from-scratch.md`
- SEO/prerendering: `https://tanstack.com/start/latest/docs/framework/react/guide/seo.md`
- Basic React Query example: `https://tanstack.com/start/latest/docs/framework/react/examples/start-basic-react-query`
- Basic Cloudflare example: `https://tanstack.com/start/latest/docs/framework/react/examples/start-basic-cloudflare`
- TanStack CLI examples are indexed from `https://github.com/tanstack/cli/blob/main/docs/examples.md`
- TanStack CLI scaffold skill docs are indexed from `https://github.com/tanstack/cli/blob/main/packages/cli/skills/create-app-scaffold/SKILL.md`

## Latest and Version-Specific Docs

For any user request involving TanStack Start API shape, setup, migration, CLI flags, deployment, or debugging, run:

```bash
npx ctx7@latest library "TanStack Start" "<full user request including version if any>"
```

Pick the best result by exact name, React framework relevance, source reputation, snippet count, and benchmark score. The best current match has been `/websites/tanstack_start_framework_react`; `/websites/tanstack_start` is broader and sometimes less focused for React Start tasks.

Then fetch the docs:

```bash
npx ctx7@latest docs /websites/tanstack_start_framework_react "<specific task>"
```

If the `library` output lists versions and the user requested one, use that versioned ID:

```bash
npx ctx7@latest docs /websites/tanstack_start_framework_react/<version> "<specific task>"
```

Only use the exact version ID shown by Context7. Do not invent version paths. On the public TanStack site, the stable current-docs path is `/start/latest/docs/framework/react/...`; verify any non-`latest` public URL before citing or using it.

For CLI flags, fetch CLI docs separately:

```bash
npx ctx7@latest docs /tanstack/cli "tanstack create flags framework deployment target-dir non-interactive"
```

If Context7 fails due to quota, say so and ask the user to authenticate with `npx ctx7@latest login` or set `CONTEXT7_API_KEY`. Do not silently fall back when the user asked for latest docs.

## Creation Commands

Interactive standard scaffold:

```bash
npx @tanstack/cli@latest create
```

Non-interactive React app:

```bash
npx @tanstack/cli@latest create app --framework React -y
```

In-place npm/Nitro scaffold when the user allows overwriting the current directory:

```bash
npx @tanstack/cli@latest create app \
  --framework React \
  --deployment nitro \
  --package-manager npm \
  --no-examples \
  --no-toolchain \
  --no-git \
  --no-intent \
  --target-dir . \
  --force \
  --non-interactive \
  --yes
```

Cloudflare deployment scaffold:

```bash
npx @tanstack/cli@latest create app --framework React --deployment cloudflare -y
```

Router-only SPA, when the user explicitly does not want TanStack Start SSR/full-stack behavior:

```bash
npx @tanstack/cli@latest create app --router-only -y
```

## Key Framework Facts

- TanStack Start is a full-stack React framework with TanStack Router, SSR, and data loading.
- The official current CLI is `@tanstack/cli`; `@tanstack/create-start` may be deprecated.
- Core dependencies in from-scratch docs include `@tanstack/react-start`, `@tanstack/react-router`, `vite`, `react`, `react-dom`, `@vitejs/plugin-react`, `typescript`, React types, DOM types, and Node types.
- In hand-written Vite config, `tanstackStart()` must come before the React plugin.
- `resolve: { tsconfigPaths: true }` is part of the documented Vite setup.
- Static prerendering is configured through `tanstackStart({ prerender: { enabled: true, crawlLinks: true } })` when appropriate.

## Current Generated Structure

Expect current CLI scaffolds to create or manage:

```text
src/
  routes/
    __root.tsx
    index.tsx
  router.tsx
  routeTree.gen.ts
  styles.css
package.json
tsconfig.json
tsr.config.json
vite.config.ts
```

The generated tree may include Nitro, Tailwind, devtools, testing libraries, public assets, and project config depending on flags.

## Editing Guidance

Root route:

- Import `HeadContent`, `Scripts`, and `createRootRoute` from `@tanstack/react-router`.
- Link app CSS via `import appCss from '../styles.css?url'` and `links: [{ rel: 'stylesheet', href: appCss }]`.
- Put metadata in `head`.
- Render `HeadContent` inside `<head>` and `Scripts` before `</body>`.
- Keep devtools only if the user wants a development surface; remove them for a clean demo site.

Homepage route:

- Use `createFileRoute('/')({ component: Home })`.
- Keep page content route-local unless it belongs in the global shell.
- For simple demo sites, prefer a single route with responsive Tailwind classes and static sections.

Route tree:

- Do not manually edit `src/routeTree.gen.ts`.
- Use `npm run generate-routes`, `npm run dev`, or `npm run build` to regenerate routes.

Styling:

- Current CLI scaffolds usually include Tailwind CSS v4 through `@tailwindcss/vite` and `@import "tailwindcss";`.
- For light-only sites, set light `body` background/foreground and remove any dark selectors or color-scheme rules.
- Check with:

```bash
grep -RIn "dark\\|prefers-color-scheme\\|color-scheme" src package.json vite.config.ts 2>/dev/null || true
```

Testing:

- Run `npm run build` after edits.
- Run `npm run test` if present. If there are no tests, add a narrow smoke test or adjust the script only when that improves the generated project rather than hiding a real failure.
- If Vitest loads Start/Vite plugins and emits CJS/ESM or hanging-server warnings for simple unit tests, add a separate `vitest.config.ts` with a plain Node environment and point the `test` script at it.

## Maintenance Checklist

Before finishing TanStack Start work:

1. Re-run docs lookup if APIs, CLI flags, deployment, or version behavior matter.
2. Inspect generated `package.json` scripts and use project-native commands.
3. Confirm the root document still has `HeadContent` and `Scripts`.
4. Confirm the React plugin order remains valid in `vite.config.ts`.
5. Confirm `src/routeTree.gen.ts` was generated, not hand-edited.
6. Run build and relevant tests.
7. If the user requested no dark mode, search the codebase for dark-mode markers and report the result.
