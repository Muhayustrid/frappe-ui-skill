<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/MobileShell/MobileShell.md -->

# MobileShell

The mobile app frame: a pinned header, a scrolling content area and a bottom
tab bar built with `MobileNav`. On desktop, use
[`DesktopShell`](desktopshell.md) instead.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import {
  MobileShell,
  MobileNav,
  MobileNavItem,
  PageHeaderMobile,
  Avatar,
} from 'frappe-ui'

const tab = ref('home')

// Long enough to overflow the content area: the point of the story is that
// only this region scrolls while the header and tab bar stay put.
// Typed as a tuple so the destructured `author` is a string, not `string | number`.
const posts = (
  [
    ['Design review: new onboarding flow', 'Priya Nair', 14, '2h'],
    ['Consolidate icon sizes to a 4px grid', 'Sam Rivera', 32, '5h'],
    ['Dark mode tokens are ready for review', 'Ana Costa', 21, '1d'],
    ['Hero copy for the marketing site', 'Liam Fischer', 5, '1d'],
    ['Illustration style guide, first draft', 'Mei Tanaka', 11, '2d'],
    ['Accessibility audit findings', 'Noah Berg', 27, '4d'],
    ['Retiring the old button variants', 'Ines Duarte', 9, '5d'],
    ['Naming conventions for new components', 'Tomas Ruiz', 16, '1w'],
    ['Figma library cleanup', 'Zara Ahmed', 4, '1w'],
    ['Empty states we still need to design', 'Priya Nair', 6, '2w'],
  ] as [string, string, number, string][]
).map(([title, author, replies, time]) => ({
  title,
  author,
  replies,
  time,
}))
</script>

<template>
  <!--
    MobileShell is `fixed inset-0`. The `transform` on this wrapper makes it the
    containing block for that fixed positioning, so the shell stays inside the
    preview box instead of covering the page. (A story-only concern.)
  -->
  <div
    class="mx-auto h-[600px] w-[360px] transform-gpu overflow-hidden border bg-surface-white"
  >
    <MobileShell>
      <PageHeaderMobile>
        <span class="text-base font-semibold text-ink-gray-8">Home</span>
      </PageHeaderMobile>

      <div class="divide-y divide-outline-gray-1">
        <div
          v-for="post in posts"
          :key="post.title"
          class="flex items-center gap-3 px-4 py-3 active:bg-surface-gray-1"
        >
          <Avatar :label="post.author" size="md" />
          <div class="min-w-0 flex-1">
            <div class="truncate text-base-medium text-ink-gray-8">
              {{ post.title }}
            </div>
            <div class="mt-1 truncate text-sm text-ink-gray-5">
              {{ post.author }} · {{ post.replies }} replies
            </div>
          </div>
          <span class="shrink-0 text-sm text-ink-gray-4">{{ post.time }}</span>
        </div>
      </div>

      <template #nav>
        <MobileNav>
          <MobileNavItem
            label="Home"
            icon="lucide-house"
            :active="tab === 'home'"
            @click="tab = 'home'"
          />
          <MobileNavItem
            label="Notifications"
            icon="lucide-bell"
            :active="tab === 'notifications'"
            @click="tab = 'notifications'"
          />
          <MobileNavItem
            label="Search"
            icon="lucide-search"
            :active="tab === 'search'"
            @click="tab = 'search'"
          />
          <MobileNavItem
            label="You"
            :active="tab === 'you'"
            @click="tab = 'you'"
          >
            <template #default="{ active }">
              <Avatar
                label="Jane Doe"
                size="md"
                :class="active ? 'ring-2 ring-outline-gray-4' : ''"
              />
            </template>
          </MobileNavItem>
        </MobileNav>
      </template>
    </MobileShell>
  </div>
</template>
```

## Anatomy

Put the routed page in the default slot and a `MobileNav` in `#nav`.
`MobileNav` is a grid. Each `MobileNavItem` in it becomes one equal-width tab,
so the bar fits any number of items.

```vue
<MobileShell>
  <RouterView />

  <template #nav>
    <MobileNav>
      <MobileNavItem label="Home" icon="lucide-house" :route="{ name: 'Home' }" />
      <MobileNavItem label="Search" icon="lucide-search" :route="{ name: 'Search' }" />
      <MobileNavItem label="You" :route="{ name: 'More' }">
        <Avatar label="Jane Doe" size="md" />
      </MobileNavItem>
    </MobileNav>
  </template>
</MobileShell>
```

## Examples

### Tab bar with an avatar tab

The last tab puts an `Avatar` in the default slot of `MobileNavItem`, in place
of the icon. The slot receives `{ active }`, so the avatar gets a ring while
its tab is active.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { MobileNav, MobileNavItem, Avatar } from 'frappe-ui'

const tab = ref('home')
</script>

<template>
  <div
    class="mx-auto w-[360px] overflow-hidden border-b border-l border-r bg-surface-white"
  >
    <MobileNav>
      <MobileNavItem
        label="Home"
        icon="lucide-house"
        :active="tab === 'home'"
        @click="tab = 'home'"
      />
      <MobileNavItem
        label="Notifications"
        icon="lucide-bell"
        :active="tab === 'notifications'"
        @click="tab = 'notifications'"
      />
      <MobileNavItem
        label="Search"
        icon="lucide-search"
        :active="tab === 'search'"
        @click="tab = 'search'"
      />
      <MobileNavItem label="You" :active="tab === 'you'" @click="tab = 'you'">
        <template #default="{ active }">
          <Avatar
            label="Jane Doe"
            size="md"
            :class="active ? 'ring-2 ring-outline-gray-4' : ''"
          />
        </template>
      </MobileNavItem>
    </MobileNav>
  </div>
</template>
```

## Behavior

### Regions

`MobileShell` is a fixed, full-height column. A page declares its own
`PageHeaderMobile`, which teleports to a `PageHeaderTarget` at the top of the
shell.

The content area uses the browser's native scrolling, with momentum and
overscroll. The header and the tab bar stay fixed to the edges. In an installed
PWA, the header gets extra top padding to clear the status bar, and the tab bar
gets bottom padding to clear the home indicator. On iOS 26 the tab bar reaches
the bottom edge only when the app loads `frappe-ui/style.css`, which gives the
installed app its full-screen viewport.

### Desktop is a separate component

`MobileShell` and `DesktopShell` are two navigation models, not one responsive
component. The app picks which one to render for the viewport.

### Scroll container

Like `DesktopShell`, the shell owns its scroll area. It provides that element
to the pages inside it and registers it in the shared registry.
`useShellScrolled({ threshold })` and `shellScrollContainer` both read it, so a
tap on the active tab or a router `scrollBehavior` can scroll it without an
app-owned global.

### Slot names

`MobileShell` has `#nav`. `DesktopShell` has `#rail` and `#sidebar`. The names
describe regions, not components. See
[`DesktopShell`](desktopshell.md#slot-names) for why they differ.

### Tab content

Each `MobileNavItem` takes a `label` and an `icon`. The default slot replaces
the icon with custom content, such as an avatar. The label always shows under
it.

### Tab links

`route` renders a router link and `href` a plain same-tab link. `route` wins
when both are set. An item with neither is a button that emits `click`.

### Tapping the current tab

When an item's `route` is already the current route, the item renders a button
instead of a link. Tapping it scrolls the shell's scroll container to the top
instead of navigating again.

### Active tab

`active` controls the highlight, separately from the current route. One tab can
stay lit across a whole section (for example, Home across every community
route), and tapping it still navigates home. Left unset, `active` is true when
`route` matches the current route.

The default slot receives `{ active }`, so custom content can react to the
highlight:

```vue
<MobileNavItem label="You" :route="{ name: 'More' }" :active="isMoreRoute">
  <template #default="{ active }">
    <Avatar label="Jane Doe" size="md" :class="{ 'ring-2 ring-outline-gray-4': active }" />
  </template>
</MobileNavItem>
```

### Tabs without a router

Like `SidebarItem`, `MobileNavItem` works without vue-router. It renders a
plain `<a>` or `<button>` with no warnings, so it works in docs, tests and
embedded use.

## Accessibility

`MobileNav` renders a `<nav>` element. Each item uses its `label` as its
accessible name. The item whose `route` is the current route gets
`aria-current="page"`.

## API reference

```ts
const mobileShellSlots = [
  {
    name: 'default',
    description: 'The routed page content, placed in the native-scrolling content area.',
    type: 'any'
  },
  {
    name: 'nav',
    description: 'The bottom navigation — usually a `MobileNav`.',
    type: 'any'
  }
]

  const mobileNavSlots = [
  {
    name: 'default',
    description: 'One or more `MobileNavItem`s, each becoming an equal-width tab.',
    type: 'any'
  }
]

  const mobileNavItemProps = [
  {
    name: 'label',
    description: 'Label under the icon; also the accessible name.',
    required: true,
    type: 'string'
  },
  {
    name: 'icon',
    description: 'Icon CSS class, e.g. `lucide-home`, or a component. Ignored when the\ndefault slot is used (for a custom glyph or an avatar).',
    required: false,
    type: 'string | Component'
  },
  {
    name: 'route',
    description: 'Navigation target. Renders a router link. Tapping the item while it is\nalready the current route scrolls the shell to the top instead of\nre-navigating.',
    required: false,
    type: 'RouteDestination'
  },
  {
    name: 'href',
    description: 'External URL. Used when `route` is absent; renders a native same-tab anchor.',
    required: false,
    type: 'string'
  },
  {
    name: 'active',
    description: 'Highlight this item. Independent of the current route so one tab can stay\nlit across a whole section (e.g. Home across community routes). Defaults to\nwhether `route` resolves to the current route.',
    required: false,
    type: 'boolean'
  }
]

  const mobileNavItemSlots = [
  {
    name: 'default',
    description: 'Custom content in place of the default icon — an avatar, a badge. Receives `{ active }`.',
    type: '{ active: boolean; }'
  }
]

  const mobileNavItemEmits = [
  {
    name: 'click',
    description: '',
    type: '[event: MouseEvent]'
  }
]
```
