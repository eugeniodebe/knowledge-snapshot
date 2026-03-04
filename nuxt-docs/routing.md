# Routing in Nuxt

Nuxt automatically generates routes from files inside `app/pages/`. No manual configuration required.

## Basic routes

```
pages/
├── index.vue          → /
├── about.vue          → /about
└── contact.vue        → /contact
```

## Dynamic routes

Parameters are defined using square brackets:

```
pages/
├── users/
│   ├── index.vue      → /users
│   └── [id].vue       → /users/123
└── blog/
    └── [slug].vue     → /blog/my-post
```

Accessing the parameter inside the component:

```vue
<script setup>
const route = useRoute()
console.log(route.params.id) // '123'
</script>
```

## Nested routes

Creating a file with the same name as its folder makes it the parent layout:

```
pages/
└── dashboard/
    ├── dashboard.vue  → parent layout (contains <NuxtPage />)
    ├── index.vue      → /dashboard
    └── settings.vue   → /dashboard/settings
```

## Catch-all routes

```
pages/
└── [...slug].vue      → /any/path/possible
```

```vue
<script setup>
const route = useRoute()
console.log(route.params.slug) // ['any', 'path', 'possible']
</script>
```

## Navigation

```vue
<template>
  <!-- Declarative -->
  <NuxtLink to="/about">Go to About</NuxtLink>
  <NuxtLink :to="{ name: 'users-id', params: { id: 42 } }">User 42</NuxtLink>

  <!-- Programmatic -->
  <button @click="navigateTo('/dashboard')">Dashboard</button>
</template>
```

## Route middleware

```ts
// middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const user = useUser()
  if (!user.value) {
    return navigateTo('/login')
  }
})
```

Apply to a page:

```vue
<script setup>
definePageMeta({
  middleware: 'auth'
})
</script>
```

## Page metadata

```vue
<script setup>
definePageMeta({
  title: 'My Page',
  layout: 'dashboard',
  middleware: ['auth'],
})

useSeoMeta({
  title: 'My Page',
  description: 'SEO description',
})
</script>
```
