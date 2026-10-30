# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Arcade Vault is an online gaming platform where users play and compete for the highest scores. Development follows **Spec Driven Design** using `/spec` and `/spec-impl` slash commands from the `Klerith/fernando-skills` skill set.

## Stack

- **Next.js 16.4.0** — see `node_modules/next/dist/docs/` for docs on this version (APIs differ from Next.js 15)
- **React 19.3.0**
- **Tailwind CSS v4** — uses `@import "tailwindcss"` and `@theme inline {}` blocks; no `tailwind.config.*` file
- **TypeScript 5**
- **Turbopack** as the default bundler in dev

## Skills

Use always /frontend-design for design UI interfaces

## Next.js 16 — Key Differences from Prior Versions

### TypeScript route helpers (no imports needed)

`PageProps<'/route'>` and `LayoutProps<'/route'>` are globally available after `next dev` or `next build` generates types. Use the route literal as the generic argument:

```tsx
// app/games/[id]/page.tsx
export default async function Page(props: PageProps<"/games/[id]">) {
  const { id } = await props.params;
}

// app/games/layout.tsx
export default function Layout({ children }: LayoutProps<"/games">) {
  return <section>{children}</section>;
}
```

### `params` and `searchParams` are Promises

In pages and layouts, `params` and `searchParams` are now `Promise` objects — always `await` them.

### `'use cache'` directive

Next.js 16 adds a first-party `'use cache'` directive (alongside React's `'use client'` and `'use server'`). Place it at the top of a file to cache all exports, or inside an async function to cache only that function. Cached functions must be `async` with serializable arguments and return values; they cannot read `cookies()`, `headers()`, or `searchParams` directly.

### Tailwind CSS v4 with Turbopack

CSS is processed via `@tailwindcss/turbopack`. Design tokens are declared with `@theme inline {}` in CSS files rather than in a JS config.

## Architecture

All routes live under `app/` (App Router only — no `pages/` directory):

```
app/
  layout.tsx   # root layout: html/body shell, Geist fonts
  page.tsx     # home route
  globals.css  # Tailwind import + CSS custom properties
```

CSS variables (`--background`, `--foreground`) are defined in `globals.css` and mapped to Tailwind tokens via `@theme inline`. Dark mode is handled with `@media (prefers-color-scheme: dark)`.
