# Server Routes in Nuxt

Nuxt includes Nitro as its built-in server. API routes are created as files inside `server/api/`.

## File structure

```
server/
├── api/
│   ├── users.get.ts       → GET  /api/users
│   ├── users.post.ts      → POST /api/users
│   └── users/
│       └── [id].delete.ts → DELETE /api/users/123
├── routes/
│   └── webhook.post.ts    → POST /webhook (no /api prefix)
└── middleware/
    └── logger.ts          → runs on every request
```

## Creating a basic endpoint

```ts
// server/api/hello.get.ts
export default defineEventHandler((event) => {
  return { message: 'Hello from the server' }
})
```

## Reading request data

```ts
// server/api/users.post.ts
export default defineEventHandler(async (event) => {
  const body = await readBody(event)
  const query = getQuery(event)
  const headers = getHeaders(event)
  const params = event.context.params // for dynamic routes

  return { received: body }
})
```

## Dynamic routes

```ts
// server/api/users/[id].get.ts
export default defineEventHandler((event) => {
  const id = getRouterParam(event, 'id')
  return { userId: id }
})
```

## Error handling

```ts
// server/api/users/[id].get.ts
export default defineEventHandler(async (event) => {
  const id = getRouterParam(event, 'id')
  const user = await db.findUser(id)

  if (!user) {
    throw createError({
      statusCode: 404,
      statusMessage: 'User not found',
    })
  }

  return user
})
```

## Server middleware

```ts
// server/middleware/auth.ts
export default defineEventHandler((event) => {
  // Runs before every request
  const token = getHeader(event, 'authorization')

  if (event.path.startsWith('/api/admin') && !token) {
    throw createError({ statusCode: 401, statusMessage: 'Unauthorised' })
  }
})
```

## Environment variables on the server

```ts
// server/api/data.get.ts
export default defineEventHandler(async (event) => {
  const config = useRuntimeConfig(event)
  // config.apiSecret — server-only
  // config.public.apiBase — available on both sides
})
```

## Server utilities with NuxtHub

With NuxtHub you can access the SQLite database and KV store directly:

```ts
// server/api/items.get.ts
export default defineEventHandler(async () => {
  const db = hubDatabase()
  const items = await db.prepare('SELECT * FROM items').all()
  return items
})
```
