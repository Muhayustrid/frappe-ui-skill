<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/DesktopShell/DesktopShell.md -->

# DesktopShell

The desktop app frame, with a `SidebarRail` and a `Sidebar` beside the page
and a pinned header above a scrolling content area. On mobile, render the
separate [`MobileShell`](mobileshell.md) component instead, as there is no
single responsive shell.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import {
  DesktopShell,
  SidebarRail,
  SidebarRailItem,
  Sidebar,
  SidebarItem,
  SidebarLabel,
  PageHeader,
  Button,
  SidebarHeader,
  Avatar,
} from 'frappe-ui'

const community = ref('design')
const communities = [
  { id: 'design', initials: 'DE' },
  { id: 'engineering', initials: 'EN' },
  { id: 'marketing', initials: 'MK' },
]

const space = ref('Website')
const spaces = [
  'Website',
  'Brand',
  'Illustration',
  'Design System',
  'Marketing Site',
  'Mobile App',
  'Research',
  'Archive',
]

// Long enough to overflow the content area: the point of the story is that
// only this region scrolls while the rail, sidebar and header stay put.
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
    ['Spacing scale: 4px or 8px base?', 'Sam Rivera', 19, '2w'],
    ['Archive: 2023 brand refresh', 'Ana Costa', 2, '3w'],
  ] as [string, string, number, string][]
).map(([title, author, replies, time]) => ({
  title,
  author,
  replies,
  time,
}))
</script>

<template>
  <!-- Bounded height so the shell's `h-full` has something to fill in the preview. -->
  <div
    class="h-[560px] overflow-hidden rounded-5 border w-full bg-surface-white"
  >
    <DesktopShell>
      <template #rail>
        <SidebarRail class="border-r">
          <SidebarRailItem
            label="Home"
            variant="ghost"
            icon="lucide-house"
            :active="community === ''"
            @click="community = ''"
          />
          <div class="flex w-full flex-1 flex-col items-center gap-3 pt-3">
            <SidebarRailItem
              v-for="c in communities"
              :key="c.id"
              :label="c.id"
              :active="community === c.id"
              @click="community = c.id"
            >
              <span class="text-2xs-medium uppercase text-ink-gray-5">{{
                c.initials
              }}</span>
            </SidebarRailItem>
          </div>
          <SidebarRailItem label="Search" variant="ghost" icon="lucide-search" />
        </SidebarRail>
      </template>

      <template #sidebar>
        <Sidebar class="border-r" :collapsible="false" width="14rem">
          <SidebarHeader
            title="Acme Design"
            subtitle="v1.0.0-beta"
            :menu-items="[{ label: 'Log out', icon: 'lucide-log-out' }]"
          />
          <div class="flex-1 overflow-y-auto space-y-0.5 px-2 pb-2">
            <SidebarLabel class="px-2">Spaces</SidebarLabel>
            <SidebarItem
              v-for="s in spaces"
              :key="s"
              :active="s === space"
              @click="space = s"
            >
              <template #prefix
                ><span class="lucide-hash size-4" aria-hidden="true"
              /></template>
              {{ s }}
            </SidebarItem>
          </div>
        </Sidebar>
      </template>

      <!-- Declared in the page; teleports to the target the shell pins on top. -->
      <PageHeader>
        <span class="text-lg font-semibold text-ink-gray-8">{{ space }}</span>
        <Button variant="subtle" icon-left="lucide-plus" label="New post" />
      </PageHeader>

      <div class="divide-y divide-outline-gray-1">
        <div
          v-for="post in posts"
          :key="post.title"
          class="flex items-center gap-3 px-5 py-3 hover:bg-surface-gray-1"
        >
          <Avatar :label="post.author" size="lg" />
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
    </DesktopShell>
  </div>
</template>
```

## Examples

### List and detail

`:scroll="false"` turns off the page scroll, so the ticket list and the ticket
each scroll on their own.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { Avatar, DesktopShell, PageHeader, ScrollArea } from 'frappe-ui'

const tickets = Array.from({ length: 20 }, (_, i) => ({
  id: 1040 + i,
  subject: [
    'Cannot reset my password',
    'Invoice shows the wrong tax rate',
    'Export to CSV times out',
    'Add a second admin to our account',
    'Emails land in spam',
  ][i % 5],
  customer: ['Priya Nair', 'Sam Rivera', 'Ana Costa', 'Liam Fischer'][i % 4],
}))

const selectedId = ref(tickets[0].id)
const selected = computed(
  () => tickets.find((t) => t.id === selectedId.value) ?? tickets[0],
)
</script>

<template>
  <div
    class="h-[480px] w-full overflow-hidden rounded-5 border bg-surface-base"
  >
    <!-- scroll=false: each pane scrolls on its own. -->
    <DesktopShell :scroll="false">
      <PageHeader>
        <span class="text-lg font-semibold text-ink-gray-8">Tickets</span>
      </PageHeader>

      <div class="flex min-h-0 flex-1">
        <ScrollArea class="w-72 shrink-0 border-r">
          <div class="p-2">
            <button
              v-for="t in tickets"
              :key="t.id"
              type="button"
              class="flex w-full flex-col gap-1 rounded-4 px-3 py-2 text-left"
              :class="
                t.id === selectedId
                  ? 'bg-surface-gray-3'
                  : 'hover:bg-surface-gray-2'
              "
              @click="selectedId = t.id"
            >
              <span class="truncate text-base-medium text-ink-gray-8">
                {{ t.subject }}
              </span>
              <span class="text-sm text-ink-gray-5">
                #{{ t.id }} · {{ t.customer }}
              </span>
            </button>
          </div>
        </ScrollArea>

        <ScrollArea class="flex-1">
          <div class="space-y-4 p-5">
            <div class="flex items-center gap-2">
              <Avatar :label="selected.customer" size="md" />
              <span class="text-base-medium text-ink-gray-8">
                {{ selected.customer }}
              </span>
            </div>
            <div class="text-xl font-semibold text-ink-gray-9">
              {{ selected.subject }}
            </div>
            <p v-for="n in 8" :key="n" class="text-p-base text-ink-gray-7">
              Hi team, this started after the last update. I have tried the
              steps in the help article, but the problem is still there. Can you
              take a look?
            </p>
          </div>
        </ScrollArea>
      </div>
    </DesktopShell>
  </div>
</template>
```

## Behavior

### Regions

Put the icon column in `#rail`, the navigation panel in `#sidebar`, and the
routed page in the default slot. Both side slots are optional. Render
`#sidebar` with `v-if` to hide it on routes that don't need it.

A page declares its own `PageHeader` anywhere in its template. The header
teleports to a `PageHeaderTarget` that the shell pins above the scroll area.

### Navigation side

`navSide="right"` draws the rail and the sidebar on the right edge of the
shell. Only the drawing is reversed: the DOM order stays rail, sidebar, page,
so Tab reaches the navigation first on both sides. A `SidebarRailItem` in the
rail opens its tooltip toward the page and puts its active bar on the outer
edge. A collapsed `SidebarItem` opens its tooltip toward the page too.

```vue
<DesktopShell nav-side="right">
  <template #rail><SidebarRail>...</SidebarRail></template>
  <template #sidebar><Sidebar /></template>
</DesktopShell>
```

`navSide` is a physical side, `left` or `right`, and assumes a left-to-right
page. If your app stores "start" and "end", map them to a physical side in
one place. The shell mirrors nothing else: borders, resize handles and chevrons
are yours to flip.

### Scroll container

The shell owns its scroll area. `useShellScrolled()` reads it with no setup,
so a page can scroll to the top and a router `scrollBehavior` can read the
scroll offset without an app-owned global.

```ts
import { shellScrollContainer, useShellScrolled } from 'frappe-ui'

const scrolled = useShellScrolled({ threshold: 12 })
shellScrollContainer.value?.scrollTo({ top: 0, behavior: 'smooth' })
```

`useShellScrolled()` in a page reads the shell that page is inside, even while
another shell is still mounted. `shellScrollContainer` is for code that is not inside the shell: a
router `scrollBehavior`, a navigation guard, or code outside a component. It
holds a stack, so when `DesktopShell` and `MobileShell` swap on a viewport
change, the active container passes to the new shell in any mount order.

### Turning the page scroll off

`:scroll="false"` makes the content area fill the remaining height and never
scroll as a page. Use it in a layout where the panes own their overflow: a
list-and-detail split, or a board whose columns scroll separately. It replaces
workarounds such as `absolute inset-0`, a fixed `h-[calc(100vh-3rem)]`, or
`[&>div]:h-full`.

```vue
<DesktopShell :scroll="false">
  <template #sidebar><Sidebar /></template>
  <div class="flex min-h-0 flex-1">
    <ScrollArea class="w-72 border-e"><ItemList /></ScrollArea>
    <ScrollArea class="flex-1"><Detail /></ScrollArea>
  </div>
</DesktopShell>
```

With `:scroll="false"` the shell has no scroll element, so
`shellScrollContainer` is `null` and `useShellScrolled()` stays `false`. Read
the pane's own `ScrollArea` instead.

### Slot names

`DesktopShell` has `#rail` and `#sidebar`. `MobileShell` has `#nav`. The names
describe regions, not components. The desktop frame has two side regions that
can appear together, and the mobile frame has one bar along the bottom. One
shared name would have to mean "the icon column", "the navigation panel" and
"the tab bar" at once, and an app that renders both a rail and a sidebar could
not say which is which.

## API reference

```ts
const propsData = [
  {
    name: 'scroll',
    description: 'Whether the content area scrolls as one page. Defaults to `true`.\n\nPass `false` for a multi-pane layout where the panes own their own\noverflow — a list-and-detail split, or a board whose columns scroll\nseparately. The content area then fills the remaining height and never\npage-scrolls, instead of the app faking it with `absolute inset-0` or a\nhardcoded `h-[calc(100vh-3rem)]`.\n\n`false` also means there is no shell scroll element, so\n`shellScrollContainer` stays `null` and `useShellScrolled()` stays `false`.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'navSide',
    description: 'The physical edge the navigation (rail and sidebar) sits on. Defaults to\n`\'left\'`.\n\n`\'right\'` draws the shell right to left with `flex-row-reverse`. The DOM\norder stays rail, sidebar, content, so Tab reaches the navigation first on\nboth sides. `SidebarRailItem` reads the value: its tooltip opens away from\nthe navigation and its active bar sits on the outer edge.\n\nIt is a physical side and assumes a left-to-right page. Map "start" and\n"end" onto it in the app. `SidebarRailItem` and a collapsed\n`SidebarItem` read it for their tooltips. Borders and handles are not\nmirrored.',
    required: false,
    type: '"left" | "right"',
    default: '"left"'
  }
]

  const slotsData = [
  {
    name: 'rail',
    description: 'The icon column — usually a `SidebarRail`. Omit it on shells that don\'t use one.',
    type: 'any'
  },
  {
    name: 'sidebar',
    description: 'The navigation panel — usually a `Sidebar`. Render it conditionally to hide it on routes that don\'t need it.',
    type: 'any'
  },
  {
    name: 'default',
    description: 'The routed page content, placed in the scroll region (or the fixed-height pane when `scroll` is `false`).',
    type: 'any'
  }
]
```
