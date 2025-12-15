---
title: 'Vue 3 Composition API: Modern Component Development'
description: 'Master Vue 3 Composition API for better code organization and TypeScript integration'
pubDate: 'Dec 15 2025'
heroImage: '/blog-placeholder-1.jpg'
---

Vue 3's Composition API revolutionizes component development with better logic reusability, TypeScript support, and clearer code organization. Let's explore this powerful new approach.

## What is the Composition API?

The Composition API is an alternative to the Options API, offering a more flexible way to compose component logic using functions instead of options.

**Options API (Vue 2 style):**
```javascript
export default {
  data() {
    return { count: 0 }
  },
  methods: {
    increment() {
      this.count++
    }
  },
  computed: {
    double() {
      return this.count * 2
    }
  }
}
```

**Composition API (Vue 3):**
```javascript
import { ref, computed } from 'vue'

export default {
  setup() {
    const count = ref(0)
    const double = computed(() => count.value * 2)
    
    function increment() {
      count.value++
    }
    
    return { count, double, increment }
  }
}
```

## Setup Function

The `setup()` function is the entry point for Composition API:

```vue
<script>
import { ref, onMounted } from 'vue'

export default {
  props: {
    userId: String
  },
  setup(props, context) {
    // Access props
    console.log(props.userId)
    
    // Access context
    // context.attrs, context.slots, context.emit, context.expose
    
    const user = ref(null)
    
    onMounted(() => {
      console.log('Component mounted')
    })
    
    // Return exposes to template
    return { user }
  }
}
</script>
```

## Script Setup Syntax

The `<script setup>` provides a more concise syntax:

```vue
<script setup>
import { ref, computed } from 'vue'

// No need for setup() function
// No need to return - everything is auto-exposed

const count = ref(0)
const double = computed(() => count.value * 2)

function increment() {
  count.value++
}
</script>

<template>
  <div>
    <p>Count: {{ count }}</p>
    <p>Double: {{ double }}</p>
    <button @click="increment">Increment</button>
  </div>
</template>
```

## Reactive State

**ref()** - For primitive values:
```javascript
import { ref } from 'vue'

const count = ref(0)
const message = ref('Hello')

// Access/modify with .value
count.value++
console.log(count.value) // 1

// In template, .value is auto-unwrapped
// <p>{{ count }}</p> works without .value
```

**reactive()** - For objects:
```javascript
import { reactive } from 'vue'

const state = reactive({
  count: 0,
  message: 'Hello',
  user: {
    name: 'Alice',
    age: 28
  }
})

// No .value needed
state.count++
state.user.name = 'Bob'

// Destructuring loses reactivity!
let { count } = state // ❌ Not reactive

// Use toRefs for reactive destructuring
import { toRefs } from 'vue'
const { count, message } = toRefs(state) // ✅ Reactive
```

## Computed Properties

```javascript
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

// Read-only computed
const fullName = computed(() => {
  return `${firstName.value} ${lastName.value}`
})

// Writable computed
const fullName = computed({
  get() {
    return `${firstName.value} ${lastName.value}`
  },
  set(value) {
    [firstName.value, lastName.value] = value.split(' ')
  }
})

fullName.value = 'Jane Smith'
```

## Watchers

**watch()** - Watch specific sources:
```javascript
import { ref, watch } from 'vue'

const count = ref(0)
const message = ref('')

// Watch single ref
watch(count, (newVal, oldVal) => {
  console.log(`Count changed from ${oldVal} to ${newVal}`)
})

// Watch multiple sources
watch([count, message], ([newCount, newMsg], [oldCount, oldMsg]) => {
  console.log('Something changed')
})

// Watch object property
const state = reactive({ count: 0 })
watch(
  () => state.count,
  (newVal) => console.log(newVal)
)

// With options
watch(count, (newVal) => {
  // ...
}, {
  immediate: true, // Run immediately
  deep: true       // Deep watch for objects
})
```

**watchEffect()** - Auto-track dependencies:
```javascript
import { ref, watchEffect } from 'vue'

const count = ref(0)
const message = ref('Hello')

// Automatically tracks dependencies
watchEffect(() => {
  console.log(`Count is ${count.value}`)
  // Will re-run when count changes
})

// Stop watching
const stop = watchEffect(() => {
  // ...
})

stop() // Stop the watcher
```

## Lifecycle Hooks

```javascript
import {
  onBeforeMount,
  onMounted,
  onBeforeUpdate,
  onUpdated,
  onBeforeUnmount,
  onUnmounted
} from 'vue'

export default {
  setup() {
    onBeforeMount(() => {
      console.log('Before mount')
    })
    
    onMounted(() => {
      console.log('Mounted')
    })
    
    onBeforeUpdate(() => {
      console.log('Before update')
    })
    
    onUpdated(() => {
      console.log('Updated')
    })
    
    onBeforeUnmount(() => {
      console.log('Before unmount')
    })
    
    onUnmounted(() => {
      console.log('Unmounted')
    })
  }
}
```

## Composables (Reusable Logic)

Create reusable stateful logic:

```javascript
// composables/useMouse.js
import { ref, onMounted, onUnmounted } from 'vue'

export function useMouse() {
  const x = ref(0)
  const y = ref(0)
  
  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }
  
  onMounted(() => {
    window.addEventListener('mousemove', update)
  })
  
  onUnmounted(() => {
    window.removeEventListener('mousemove', update)
  })
  
  return { x, y }
}

// Use in component
<script setup>
import { useMouse } from './composables/useMouse'

const { x, y } = useMouse()
</script>

<template>
  <p>Mouse position: {{ x }}, {{ y }}</p>
</template>
```

**Fetch composable:**
```javascript
// composables/useFetch.js
import { ref } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)
  const loading = ref(false)
  
  async function fetch() {
    loading.value = true
    data.value = null
    error.value = null
    
    try {
      const response = await fetch(url)
      data.value = await response.json()
    } catch (e) {
      error.value = e.message
    } finally {
      loading.value = false
    }
  }
  
  fetch()
  
  return { data, error, loading, refetch: fetch }
}
```

## Props and Emits

```vue
<script setup>
import { computed } from 'vue'

// Define props
const props = defineProps({
  title: {
    type: String,
    required: true
  },
  count: {
    type: Number,
    default: 0
  }
})

// Define emits
const emit = defineEmits(['update', 'delete'])

// Use props
const displayTitle = computed(() => props.title.toUpperCase())

// Emit events
function handleUpdate() {
  emit('update', props.count + 1)
}

function handleDelete() {
  emit('delete', props.count)
}
</script>
```

## Template Refs

Access DOM elements:

```vue
<script setup>
import { ref, onMounted } from 'vue'

const inputRef = ref(null)
const listRef = ref([])

onMounted(() => {
  // Access DOM element
  inputRef.value.focus()
  
  // Access multiple elements
  console.log(listRef.value.length)
})
</script>

<template>
  <input ref="inputRef" />
  
  <ul>
    <li v-for="item in items" :key="item" :ref="el => listRef.push(el)">
      {{ item }}
    </li>
  </ul>
</template>
```

## Provide / Inject

Share data across component tree:

```vue
<!-- Parent -->
<script setup>
import { provide, ref } from 'vue'

const theme = ref('dark')
provide('theme', theme)

function toggleTheme() {
  theme.value = theme.value === 'dark' ? 'light' : 'dark'
}
provide('toggleTheme', toggleTheme)
</script>

<!-- Child (any level deep) -->
<script setup>
import { inject } from 'vue'

const theme = inject('theme')
const toggleTheme = inject('toggleTheme')
</script>

<template>
  <div :class="theme">
    <button @click="toggleTheme">Toggle Theme</button>
  </div>
</template>
```

## TypeScript Integration

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

interface User {
  id: number
  name: string
  email: string
}

const user = ref<User | null>(null)
const users = ref<User[]>([])

// Props with types
interface Props {
  title: string
  count?: number
}

const props = withDefaults(defineProps<Props>(), {
  count: 0
})

// Emits with types
const emit = defineEmits<{
  update: [id: number]
  delete: [id: number]
}>()

// Computed with type
const userName = computed<string>(() => {
  return user.value?.name ?? 'Guest'
})
</script>
```

## Best Practices

1. **Use `<script setup>`** for cleaner syntax
2. **Group related logic** together in setup
3. **Extract reusable logic** into composables
4. **Use ref() for primitives**, reactive() for objects
5. **Always clean up** side effects in onUnmounted
6. **Type your code** with TypeScript for better DX

The Composition API makes Vue 3 more powerful and flexible. Start by converting small components, create reusable composables, and embrace the new patterns for better code organization.
