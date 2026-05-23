---
name: iw-cloudflare
description: Install, inspect, repair, update, and use the official Cloudflare Skills bundle for AI agents. Use when a user asks for ready-made or official Cloudflare/Wrangler/Workers/Pages skills, wants to query the latest Cloudflare skills without installing old copies, repair missing Cloudflare skills, update installed Cloudflare skills, log in to Cloudflare, or deploy a static website to Cloudflare Pages/Workers.
---

# iw-cloudflare

Manage Cloudflare's official Skills bundle and give basic Cloudflare deployment guidance. Prefer official, latest upstream sources over hand-written replacement skills.

## Official source

Cloudflare's official Skills repository is:

```text
https://github.com/cloudflare/skills
```

Never install from an old local clone when the user asks for current Cloudflare skills. Use `npx skills` so the latest CLI resolves and fetches the upstream repository.

## Query latest available Cloudflare skills

List the skills currently published by Cloudflare without installing them:

```bash
npx skills add https://github.com/cloudflare/skills --list
```

Use this before install/update when the user asks what is available, says "latest", or wants to avoid old skills. Summarize the listed skill names and call out the ones relevant to the task.

## Install latest official bundle

Install all current Cloudflare skills for all supported agents, non-interactively:

```bash
npx skills add https://github.com/cloudflare/skills --all
```

For global/user-level install instead of project-level install:

```bash
npx skills add https://github.com/cloudflare/skills --all --global
```

If npm asks to install the `skills` package, answer `y` unless the user requested dry-run only.

## Verify and repair missing skills

Check installed skills:

```bash
npx skills list --json
npx skills list --global --json
```

Expected Cloudflare bundle skills commonly include:

- `cloudflare`
- `wrangler`
- `workers-best-practices`
- `web-perf`
- `durable-objects`
- `agents-sdk`
- `cloudflare-email-service`
- `sandbox-sdk`

Repair missing Cloudflare skills by re-running the official install command; it should restore missing skills from the latest upstream bundle:

```bash
npx skills add https://github.com/cloudflare/skills --all
```

If the user names specific missing skills, install only those from the official repository:

```bash
npx skills add https://github.com/cloudflare/skills --skill cloudflare wrangler workers-best-practices -y
```

Use `--global` too if the missing skills are expected in global scope.

## Update installed Cloudflare skills

Update installed skills to latest versions:

```bash
npx skills update -y
```

Update global skills:

```bash
npx skills update --global -y
```

If the user specifically asks to refresh Cloudflare skills and update does not find them, query upstream with `--list`, then repair by re-running the official `add` command.

## Basic Cloudflare account and auth guidance

1. Create or use a Cloudflare account at the Cloudflare dashboard.
2. Install/use Wrangler through the project, usually with `npx wrangler@latest ...` or a project `npm run deploy` script.
3. Authorize Wrangler with OAuth:

```bash
npx wrangler@latest login
```

This opens a browser login/authorization page. Complete the Cloudflare sign-in and approve the requested permissions. To verify authentication:

```bash
npx wrangler@latest whoami
```

For CI or headless environments, use Cloudflare API tokens instead of browser login. Do not ask users to paste secrets into prompts; tell them to store tokens in their shell, CI secret store, or Cloudflare-supported secret mechanism.

## Deploy a static website / Page

Prefer the user's existing project scripts when present:

```bash
npm run build
npm run deploy
```

For a built static output directory such as `dist`, the current Cloudflare path is Workers Static Assets via latest Wrangler:

```bash
npx wrangler@latest deploy --assets ./dist --name my-static-site
```

For first-time static asset deployment, Wrangler may generate or update `wrangler.jsonc`. This is the recommended CLI path for a static page/site from local files. A minimal static assets config looks like:

```jsonc
{
  "name": "my-static-site",
  "compatibility_date": "2026-05-20",
  "assets": {
    "directory": "dist"
  }
}
```

Then future deploys can usually run:

```bash
npx wrangler@latest deploy
```

For dashboard/Git-based Cloudflare Pages deployment, connect the repository in the Cloudflare dashboard, set the build command such as `npm run build`, and set the output directory such as `dist`, `build`, or `out` according to the framework. Use Wrangler/local deploy when the user wants CLI deployment from the current machine.

## Create a new static/full-stack Cloudflare project

For a strong static site foundation with Worker/backend expansion ability:

```bash
npm create cloudflare@latest my-cloudflare-site -- --type hello-world-with-assets --git --agents --accept-defaults
```

For static assets only:

```bash
npm create cloudflare@latest my-static-site -- --type hello-world-assets-only --git --agents --accept-defaults
```

Then:

```bash
cd my-cloudflare-site
npm run dev
npm run deploy
```

## Operational rules

- Use official Cloudflare docs or `ctx7` before changing commands that may have changed.
- Query with `--list` when freshness matters.
- Prefer `npx wrangler@latest` for one-off commands to avoid stale globally installed Wrangler.
- Do not claim deployment succeeded until the command completes and prints the deployed URL or success output.
- If install/update fails due to network, npm, GitHub, or auth errors, report the exact error and the next corrective command.
