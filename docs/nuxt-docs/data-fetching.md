# Data Fetching in Nuxt

Nuxt provides three main ways to fetch data, each optimised for different use cases.

## useFetch

The simplest approach. Combines `useAsyncData` + `$fetch` into a single composable:

```vue
<script setup>
const { data, status, error, refresh } = await useFetch('/api/users')
</script>

<template>
  <div v-if="status === 'pending'">Loading...</div>
  <div v-else-if="error">Error: {{ error.message }}</div>
  <ul v-else>
    <li v-for="user in data" :key="user.id">{{ user.name }}</li>
  </ul>
</template>
```

With options:

```vue
<script setup>
const { data } = await useFetch('/api/users', {
  method: 'POST',
  body: { name: 'John' },
  headers: { Authorization: 'Bearer token' },
  query: { page: 1, limit: 10 },
  pick: ['id', 'name'],         // Only extract these fields
  transform: (data) => data.users,
})
</script>
```

## useAsyncData

For more complex cases or when you're not calling a URL directly:

```vue
<script setup>
const { data: users } = await useAsyncData('users', async () => {
  const [list, count] = await Promise.all([
    $fetch('/api/users'),
    $fetch('/api/users/count'),
  ])
  return { list, count }
})
</script>
```

The key (`'users'`) prevents the same call from running multiple times if the component mounts more than once.

## $fetch

For imperative calls (event handlers, without automatic SSR):

```vue
<script setup>
async function createUser() {
  const user = await $fetch('/api/users', {
    method: 'POST',
    body: { name: 'Jane', email: 'jane@example.com' },
  })
  console.log(user)
}
</script>
```

## Lazy loading

By default `useFetch` and `useAsyncData` block navigation until data is ready. To avoid blocking:

```vue
<script setup>
// Does not block navigation — renders the page immediately
const { data, status } = useLazyFetch('/api/users')
</script>
```

## Reactivity — fetch on parameter change

When the fetch depends on a reactive variable, pass a function or ref:

```vue
<script setup>
const page = ref(1)

// Automatically re-runs when `page` changes
const { data } = await useFetch('/api/users', {
  query: { page },
})
</script>
```

## Key differences

| | `useFetch` | `useAsyncData` | `$fetch` |
|---|---|---|---|
| Automatic SSR | Yes | Yes | No |
| Deduplication | Yes | Yes | No |
| Best for | Direct URL calls | Complex logic | Handlers, actions |
