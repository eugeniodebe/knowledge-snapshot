# State Management in Nuxt

Nuxt provides built-in tools for managing state without external libraries like Pinia (though Pinia is fully supported too).

## useState

`useState` is an SSR-friendly composable that replaces `ref` for shared state across components:

```ts
// composables/useCounter.ts
export const useCounter = () => useState('counter', () => 0)
```

```vue
<!-- ComponentA.vue -->
<script setup>
const counter = useCounter()
</script>

<template>
  <button @click="counter++">{{ counter }}</button>
</template>
```

```vue
<!-- ComponentB.vue — shares the same state -->
<script setup>
const counter = useCounter()
</script>

<template>
  <p>Current value: {{ counter }}</p>
</template>
```

The key (`'counter'`) identifies the state globally. The initial value is only used if the state doesn't exist yet.

## Composables as stores

The recommended Nuxt pattern is to create composables that encapsulate state and logic:

```ts
// composables/useUser.ts
export const useUser = () => {
  const user = useState<User | null>('user', () => null)

  async function login(email: string, password: string) {
    user.value = await $fetch('/api/auth/login', {
      method: 'POST',
      body: { email, password },
    })
  }

  function logout() {
    user.value = null
    navigateTo('/login')
  }

  const isLoggedIn = computed(() => !!user.value)

  return { user: readonly(user), login, logout, isLoggedIn }
}
```

## Pinia (optional)

If you prefer Pinia, the official module is available:

```bash
npx nuxi@latest module add pinia
```

```ts
// stores/cart.ts
export const useCartStore = defineStore('cart', () => {
  const items = ref<CartItem[]>([])
  const total = computed(() => items.value.reduce((sum, i) => sum + i.price, 0))

  function addItem(item: CartItem) {
    items.value.push(item)
  }

  return { items, total, addItem }
})
```

## When to use each option

| Option | When to use |
|--------|------------|
| `ref` / `reactive` | Local component state |
| `useState` | Simple shared state between components |
| Composable with `useState` | State with associated logic (recommended) |
| Pinia | Large apps, complex state, advanced devtools |

## Persistence with cookies

For state that must survive page reloads and be accessible during SSR:

```ts
// composables/useTheme.ts
export const useTheme = () => {
  const theme = useCookie('theme', { default: () => 'light' })

  function toggle() {
    theme.value = theme.value === 'light' ? 'dark' : 'light'
  }

  return { theme: readonly(theme), toggle }
}
```

`useCookie` automatically syncs between server and client with no async hydration needed.
