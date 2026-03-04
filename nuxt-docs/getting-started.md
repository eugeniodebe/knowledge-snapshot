# Getting Started with Nuxt.js

Nuxt is a full-stack framework built on Vue.js that provides server-side rendering (SSR), static site generation (SSG), and built-in API routes.

## Requirements

- Node.js 18.0 or higher
- npm, pnpm, yarn or bun

## Create a new project

```bash
npx nuxi@latest init my-app
cd my-app
npm install
npm run dev
```

The development server will be available at `http://localhost:3000`.

## Project structure

```
my-app/
├── app/
│   ├── pages/          # Automatic file-based routes
│   ├── components/     # Vue components (auto-imported)
│   ├── composables/    # Reusable logic (auto-imported)
│   ├── layouts/        # Page layouts
│   └── app.vue         # Root component
├── server/
│   ├── api/            # API routes
│   └── middleware/     # Server middleware
├── public/             # Static assets
└── nuxt.config.ts      # Nuxt configuration
```

## Basic configuration

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  devtools: { enabled: true },
  modules: [],
  runtimeConfig: {
    // Variables only available on the server
    apiSecret: process.env.API_SECRET,
    public: {
      // Variables available on both client and server
      apiBase: process.env.API_BASE_URL,
    },
  },
})
```

## Environment variables

Nuxt automatically exposes environment variables prefixed with `NUXT_`:

```env
NUXT_API_SECRET=my-secret
NUXT_PUBLIC_API_BASE=https://api.example.com
```

## Rendering modes

| Mode | Config | Description |
|------|--------|-------------|
| SSR (default) | `ssr: true` | Server-rendered on every request |
| SSG | `nitro.prerender` | Pre-rendered at build time |
| SPA | `ssr: false` | Client-only, no server rendering |
| Hybrid | `routeRules` | Per-route mix of SSR/SSG/SPA |

```ts
// nuxt.config.ts — hybrid mode
export default defineNuxtConfig({
  routeRules: {
    '/': { prerender: true },       // Static
    '/dashboard/**': { ssr: false }, // SPA
    '/api/**': { cors: true },       // API with CORS
  },
})
```
