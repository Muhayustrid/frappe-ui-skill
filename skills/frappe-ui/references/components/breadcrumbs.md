<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Breadcrumbs/Breadcrumbs.md -->

# Breadcrumbs

A row of links that shows where the current page sits in a hierarchy, and lets
the user go back to a parent page.



## Examples

### Page header

Breadcrumbs on the left of a page header, with page actions on the right. The
last item is the current page. The items here navigate with `onClick`.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Breadcrumbs, Button } from 'frappe-ui'

const lastClicked = ref('')

const items = [
  { label: 'Projects', onClick: () => (lastClicked.value = 'Projects') },
  {
    label: 'Website redesign',
    onClick: () => (lastClicked.value = 'Website redesign'),
  },
  { label: 'Homepage copy' },
]
</script>

<template>
  <div class="w-full max-w-2xl">
    <header
      class="flex h-12 items-center justify-between gap-4 border-b border-outline-gray-1 px-4"
    >
      <Breadcrumbs :items="items" />
      <div class="flex shrink-0 items-center gap-2">
        <Button icon-left="lucide-share-2">Share</Button>
        <Button variant="solid" icon-left="lucide-plus">New task</Button>
      </div>
    </header>
    <p class="px-4 pt-3 text-p-sm text-ink-gray-5">
      {{ lastClicked ? `Opened ${lastClicked}` : 'Click a parent page' }}
    </p>
  </div>
</template>
```

### Path to a person

The `#prefix` slot shows an icon or an avatar before each label. The slot reads
`icon` and `user`, which are extra fields on the items.

```vue
<script setup lang="ts">
import { Avatar, Breadcrumbs } from 'frappe-ui'

const email = 'jane.cooper@example.com'

const items = [
  { label: 'Teams', icon: 'lucide-users', onClick: () => {} },
  { label: 'Design', icon: 'lucide-palette', onClick: () => {} },
  {
    label: 'Jane Cooper',
    user: { image: `https://i.pravatar.cc/80?u=${email}` },
  },
]
</script>

<template>
  <Breadcrumbs :items="items">
    <template #prefix="{ item }">
      <Avatar
        v-if="item.user"
        :image="item.user.image"
        :label="item.label"
        size="sm"
        class="mr-1.5"
      />
      <span
        v-else
        :class="item.icon"
        class="mr-1.5 size-4 shrink-0"
        aria-hidden="true"
      />
    </template>
  </Breadcrumbs>
</template>
```

## Behavior

### Item shape

Each item needs a `label`. How the item navigates depends on the other fields:

- `route` renders a `<router-link>`.
- `href` renders an `<a>`.
- Neither renders a `<button>`, so use `onClick` to navigate.

The last item is the current page. Its label truncates when space runs out,
and the full label shows on hover.

### Links that navigate in the app

Set `href` and `onClick` together when the app navigates by itself but the crumb
must still be a real link. A plain left click runs `onClick` only. A click with
<kbd>Cmd</kbd>, <kbd>Ctrl</kbd> or <kbd>Shift</kbd>, and a middle click, stay
with the browser and open the URL. The context menu keeps "Copy link address".

```js
const items = [
  {
    label: 'Dashboards',
    href: '/app/dashboard',
    onClick: () => router.push('/dashboard'),
  },
  { label: 'Sales' },
]
```

### Extra fields

An item also carries any extra field you put on it. `BreadcrumbItem` has an
open index signature, so a crumb can hold the record it came from and the
`#prefix` and `#suffix` slots can read it back:

```vue
<Breadcrumbs :items="items">
  <template #prefix="{ item }">
    <Avatar v-if="item.user" :image="item.user.avatar" size="sm" />
  </template>
</Breadcrumbs>
```

`#prefix` renders before the crumb's label and `#suffix` after it. Each
receives `{ item }`, and they render for every crumb.

### Long paths

When the items do not fit their container and there are more than two, all
items except the last two move into a menu behind a "…" button. The menu opens
each item with its `onClick` or `route`. An item with only `href` does nothing
in that menu, so give it an `onClick` too.

## Accessibility

- Breadcrumbs does not add a landmark. Wrap it in
  `<nav aria-label="Breadcrumb">` when it is the page's main breadcrumb trail.
- The `/` separators are hidden from screen readers.

## API reference

```ts
const propsData = [
  {
    name: 'items',
    description: 'Ordered list of breadcrumb items',
    required: true,
    type: 'BreadcrumbItem[]'
  }
]

  const slotsData = [
  {
    name: 'prefix',
    description: 'Content shown before each breadcrumb label',
    type: '{ item: BreadcrumbItem; }'
  },
  {
    name: 'suffix',
    description: 'Content shown after each breadcrumb label',
    type: '{ item: BreadcrumbItem; }'
  }
]
```
