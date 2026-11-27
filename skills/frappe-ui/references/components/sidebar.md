<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Sidebar/Sidebar.md -->

# Sidebar

The wide navigation column of an app. You fill it with `SidebarItem`,
`SidebarSection` and your own markup.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import {
  Sidebar,
  SidebarItem,
  SidebarLabel,
  ScrollArea,
  Button,
  Dropdown,
} from 'frappe-ui'

// A faithful Gameplan sidebar: an app switcher up top, then a scrolling list of
// spaces with lucide icons, unread counts, and a hover-reveal
// options menu. Only <Sidebar>/<SidebarItem>/<SidebarLabel> come from the
// family — the header, the ScrollArea, and the spaces markup are the app's own.
const active = ref('product')
const sort = ref('Recent activity')

const spaces = [
  { id: 'product', title: 'Product', icon: 'lucide-rocket', unread: 0 },
  { id: 'design', title: 'Design', icon: 'lucide-palette', unread: 3 },
  { id: 'engineering', title: 'Engineering', icon: 'lucide-code', unread: 12 },
  { id: 'marketing', title: 'Marketing', icon: 'lucide-megaphone', unread: 0 },
  { id: 'sales', title: 'Sales', icon: 'lucide-trending-up', unread: 1 },
  {
    id: 'support',
    title: 'Customer Support',
    icon: 'lucide-headphones',
    unread: 0,
  },
  { id: 'people', title: 'People & Culture', icon: 'lucide-users', unread: 0 },
  { id: 'finance', title: 'Finance', icon: 'lucide-wallet', unread: 0 },
  { id: 'leadership', title: 'Leadership', icon: 'lucide-crown', unread: 2 },
  {
    id: 'design-system',
    title: 'Design System',
    icon: 'lucide-component',
    unread: 0,
  },
  {
    id: 'research',
    title: 'User Research',
    icon: 'lucide-microscope',
    unread: 5,
  },
  { id: 'ops', title: 'Operations', icon: 'lucide-settings-2', unread: 0 },
  { id: 'events', title: 'Events', icon: 'lucide-party-popper', unread: 0 },
  { id: 'data', title: 'Data & Analytics', icon: 'lucide-database', unread: 8 },
  { id: 'brand', title: 'Brand', icon: 'lucide-sparkles', unread: 0 },
  {
    id: 'partnerships',
    title: 'Partnerships',
    icon: 'lucide-handshake',
    unread: 0,
  },
  { id: 'security', title: 'Security', icon: 'lucide-shield', unread: 0 },
  {
    id: 'onboarding',
    title: 'Onboarding',
    icon: 'lucide-graduation-cap',
    unread: 0,
  },
  { id: 'random', title: 'Random', icon: 'lucide-shuffle', unread: 0 },
]

const sortOptions = [
  {
    group: 'Sort by',
    options: ['Recent activity', 'Alphabetical'].map((label) => ({
      label,
      icon: sort.value === label ? 'lucide-check' : null,
      onClick: () => (sort.value = label),
    })),
  },
]
</script>

<template>
  <div class="flex h-[560px] w-fit overflow-hidden rounded-5 border">
    <Sidebar :collapsible="false" width="14rem">
      <!-- App switcher — the app owns the header. -->
      <div class="flex shrink-0 items-center p-2">
        <button
          class="flex h-8 w-full items-center gap-2 rounded-4 px-1 transition hover:bg-surface-gray-2"
        >
          <img
            src="https://api.dicebear.com/9.x/glass/svg?seed=Lumen"
            alt=""
            class="size-6 shrink-0 rounded-4"
          />
          <span class="flex-1 truncate text-left text-base text-ink-gray-8"
            >Lumen Labs</span
          >
          <span
            class="lucide-chevrons-up-down size-4 shrink-0 text-ink-gray-5"
          />
        </button>
      </div>

      <!--
        The app owns the scroll region. frappe-ui's ScrollArea keeps the thin,
        auto-hiding overlay scrollbar; padding the viewport (px-2) gives the
        active row's shadow room so overflow-hidden doesn't clip it.
      -->
      <ScrollArea class="min-h-0 flex-1" viewport-class="px-2 pt-0.5 pb-10">
        <div class="flex h-7 items-center justify-between">
          <SidebarLabel>Spaces</SidebarLabel>
          <div class="flex items-center">
            <Dropdown :options="sortOptions" align="end">
              <template #trigger="{ open }">
                <Button
                  variant="ghost"
                  size="sm"
                  icon="lucide-arrow-up-down text-ink-gray-5"
                  label="Sort spaces"
                  tooltip="Sort spaces"
                  :active="open"
                />
              </template>
            </Dropdown>
            <Button
              variant="ghost"
              size="sm"
              icon="lucide-plus text-ink-gray-5"
              label="New space"
            />
          </div>
        </div>

        <div class="mt-0.5 space-y-0.5">
          <SidebarItem
            v-for="space in spaces"
            :key="space.id"
            :icon="space.icon"
            :active="active === space.id"
            @click="active = space.id"
          >
            <span class="flex-1 truncate text-sm">{{ space.title }}</span>

            <template #suffix>
              <!--
                Count and options menu share one cell: the count fades out on row
                hover/focus while the "…" menu fades in. The group is
                SidebarItem's root (`group/sidebar-item`). Where nothing can
                hover the trade never happens, so the cell widens and holds
                both side by side instead of one over the other.
              -->
              <div
                class="relative mr-1 flex size-7 shrink-0 items-center justify-end [@media(hover:none)]:w-auto [@media(hover:none)]:gap-1"
              >
                <span
                  v-if="space.unread > 0"
                  class="absolute right-1 text-xs text-ink-gray-5 transition-opacity group-hover/sidebar-item:opacity-0 group-focus-within/sidebar-item:opacity-0 [@media(hover:none)]:static [@media(hover:none)]:opacity-100"
                >
                  {{ space.unread }}
                </span>
                <Dropdown
                  :options="[
                    { label: 'Mark all as read' },
                    { label: 'Leave space' },
                  ]"
                  align="start"
                  side="right"
                >
                  <template #default="{ open }">
                    <Button
                      :variant="open ? 'subtle' : 'ghost'"
                      size="xs"
                      icon="lucide-more-horizontal text-ink-gray-5"
                      :label="`${space.title} options`"
                      class="absolute right-0 -mr-0.5 opacity-0 group-hover/sidebar-item:opacity-100 group-focus-within/sidebar-item:opacity-100 [@media(hover:none)]:static [@media(hover:none)]:opacity-100"
                      :class="open ? 'opacity-100' : ''"
                    />
                  </template>
                </Dropdown>
              </div>
            </template>
          </SidebarItem>
        </div>
      </ScrollArea>
    </Sidebar>
  </div>
</template>
```

## Anatomy

`Sidebar` is a fixed-width column with one slot, and it owns collapsing. It
has no layout slots and does not scroll, so lay out the header, the scrolling
list and the footer yourself with flex classes.

```vue
<Sidebar v-model:collapsed="collapsed">
  <SidebarHeader title="Acme" :menu-items="workspaceMenu" />

  <div class="flex-1 overflow-y-auto">
    <SidebarItem label="Inbox" icon="lucide-inbox" route="/inbox" />
    <SidebarLabel>Projects</SidebarLabel>
    <SidebarItem label="Website" icon="lucide-globe" route="/p/website" />

    <SidebarSection label="Archived" collapsible>
      <SidebarItem label="Q3 launch" route="/p/q3" />
    </SidebarSection>
  </div>

  <SidebarCard class="mt-auto" title="Trial ends in 5 days" :action="upgrade" />
  <SidebarCollapseToggle />
</Sidebar>
```

## Examples

### Collapsing to icons

A collapsed sidebar shows only icons. `SidebarCollapseToggle` switches between
the two widths, and `SidebarLabel divider` turns a section label into a line
while collapsed.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import {
  Sidebar,
  SidebarItem,
  SidebarLabel,
  SidebarCollapseToggle,
} from 'frappe-ui'

const collapsed = ref(true)
const active = ref('inbox')
const items = [
  { id: 'inbox', label: 'Inbox', icon: 'lucide-inbox' },
  { id: 'starred', label: 'Starred', icon: 'lucide-star' },
  { id: 'sent', label: 'Sent', icon: 'lucide-send' },
]
</script>

<template>
  <div class="flex h-[360px] w-fit overflow-hidden rounded-5 border">
    <!-- v-model:collapsed is app state; SidebarCollapseToggle flips it. -->
    <Sidebar v-model:collapsed="collapsed">
      <div class="flex-1 overflow-y-auto px-2 pt-2">
        <SidebarLabel divider>Mail</SidebarLabel>
        <SidebarItem
          v-for="item in items"
          :key="item.id"
          :icon="item.icon"
          :label="item.label"
          :active="active === item.id"
          @click="active = item.id"
        />
      </div>

      <div class="mt-auto px-2 pb-2">
        <SidebarCollapseToggle />
      </div>
    </Sidebar>
  </div>
</template>
```

### Collapsible sections

`SidebarSection` adds a label row that opens and closes the items under it.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Sidebar, SidebarSection, SidebarItem, SidebarLabel } from 'frappe-ui'

// Non-collapsible groups skip SidebarSection entirely: SidebarLabel +
// SidebarItem, composed directly. SidebarSection is only for groups that
// collapse — `viewsCollapsed` is app state its `v-model:collapsed` writes
// back to, so the app can persist the choice.
const active = ref('leads')
const viewsCollapsed = ref(false)
</script>

<template>
  <div class="flex h-[360px] w-fit overflow-hidden rounded-5 border">
    <Sidebar :collapsible="false" width="14rem">
      <div class="flex-1 overflow-y-auto px-2 pt-2">
        <SidebarLabel>Pipeline</SidebarLabel>
        <SidebarItem
          label="Leads"
          icon="lucide-user-plus"
          :active="active === 'leads'"
          @click="active = 'leads'"
        />
        <SidebarItem
          label="Deals"
          icon="lucide-handshake"
          :active="active === 'deals'"
          @click="active = 'deals'"
        />

        <SidebarSection
          label="Views"
          collapsible
          v-model:collapsed="viewsCollapsed"
        >
          <SidebarItem
            label="My Open Deals"
            icon="lucide-flame"
            :active="active === 'my-open-deals'"
            @click="active = 'my-open-deals'"
          />
          <SidebarItem
            label="Unassigned"
            icon="lucide-circle-dashed"
            :active="active === 'unassigned'"
            @click="active = 'unassigned'"
          />
        </SidebarSection>
      </div>
    </Sidebar>
  </div>
</template>
```

### Trial notice

`SidebarCard` holds a notice at the foot of the sidebar: a trial ending, an
upgrade offer, or a pointer to what is new.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { SidebarCard } from 'frappe-ui'

// Cards as they sit in a sidebar footer: on the sidebar's gray surface,
// at sidebar width. The parent owns hiding — dismiss just flips a flag.
// Actions report what they did in the status line below the grid.
const showFeatureCard = ref(true)
const status = ref('')
</script>

<template>
  <div class="grid w-full max-w-lg grid-cols-2 items-start gap-4">
    <div class="rounded-5 bg-surface-gray-1 p-3">
      <SidebarCard
        title="Your trial ends soon!"
        description="Upgrade to keep enjoying features."
        :action="{
          label: 'Update now',
          onClick: () => {
            status = 'Billing page opened'
          },
        }"
      />
    </div>

    <div class="rounded-5 bg-surface-gray-1 p-3">
      <SidebarCard
        v-if="showFeatureCard"
        theme="blue"
        dismissible
        title="New feature available"
        description="Discover the new board view for your deals."
        :action="{
          label: 'Explore now',
          onClick: ({ dismiss }) => ((status = 'Board view opened'), dismiss()),
        }"
        @dismiss="showFeatureCard = false"
      />
      <button
        v-else
        class="text-sm text-ink-gray-5 underline"
        @click="showFeatureCard = true"
      >
        Bring the card back
      </button>
    </div>

    <div class="rounded-5 bg-surface-gray-1 p-3">
      <SidebarCard
        theme="amber"
        title="Storage is almost full"
        description="Free up space or upgrade your plan."
        :action="{
          label: 'Manage storage',
          onClick: () => {
            status = 'Storage settings opened'
          },
        }"
      />
    </div>

    <div class="rounded-5 bg-surface-gray-1 p-3">
      <SidebarCard
        theme="red"
        title="Payment failed"
        description="Update your card to avoid interruption."
        :action="{
          label: 'Fix billing',
          onClick: () => {
            status = 'Card details opened'
          },
        }"
      />
    </div>

    <p v-if="status" class="col-span-2 text-sm text-ink-gray-5">
      {{ status }}
    </p>
  </div>
</template>
```

## Behavior

### Collapse

Bind `v-model:collapsed` to control collapsing. Left unbound, the sidebar
collapses by itself below the `sm` breakpoint. `:collapsible="false"` keeps it
open. `width` and `collapsedWidth` take CSS lengths.

### Items

`SidebarItem` is one row: a link or button, and a separate area after it for
`#suffix`. The suffix sits beside the link rather than inside it, so a menu
button there is valid HTML.

- `#prefix` holds a leading icon or avatar. Without it, the `icon` prop is
  used: a `lucide-*` class, an emoji or a component. Any other string shows
  nothing and logs a warning in development, so put initials in `#prefix`.
- The default slot holds the label, and falls back to the `label` prop.
- `#suffix` holds an unread count or an options menu.

`route` renders a router link and `href` a plain link. `route` wins when both
are set, and a row with neither is a button. `active` marks the current row.
Left unset, it is worked out by matching `route` against the current route.

### Header

`SidebarHeader` is the 48px row at the top, level with `PageHeader`. It shows
`title`, `subtitle`, and a square logo from `#prefix`, the `logo` prop, or the
title's first letter. `:show-logo="false"` removes the square. With
`menuItems` the header opens a dropdown, which takes the same options as
`Dropdown`. Without them it is plain text, and the logo and title stay in the
same place.

### Sections

Bind `v-model:collapsed` on a `SidebarSection` to start it closed or to
remember the choice. Left unbound, it starts open and keeps its own state.
For a group that never collapses, use `SidebarLabel` and `SidebarItem`
without a section.

### Card

`SidebarCard` does not hide itself. It emits `dismiss`, and the parent removes
it. `action` takes `Button` props and an `onClick({ dismiss })` handler, the
same shape as `Alert`'s actions. `#prefix`, `#title`, `#description` and
`#actions` replace each part.

### Attributes

Attributes on `SidebarItem` go to two places. `class`, `style` and event
listeners go on the row, so a background or a drop target covers the
`#suffix` area too. Every other attribute (`target`, `rel`, `id`, `title`,
`data-*`, `aria-*`) goes on the link or button inside. Your `aria-label`
replaces the one taken from the label.

## Accessibility

`Sidebar` renders a `<nav>` named "Main". Set `ariaLabel` to rename it, when a
page has a second sidebar or to translate it. It is the only landmark in the
family, so keep your own wrappers as plain `div`s.

`SidebarSection` marks its items as a group, named by its label. A collapsible
section's toggle reports whether it is open.

## API reference

```ts
const sidebarProps = [
  {
    name: 'collapsible',
    description: 'Allows the sidebar to collapse. Default: `true`.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'width',
    description: 'Expanded width as a CSS length. Applied inline so apps can override it.',
    required: false,
    type: 'string',
    default: '"15rem"'
  },
  {
    name: 'collapsedWidth',
    description: 'Collapsed width as a CSS length.',
    required: false,
    type: 'string',
    default: '"3rem"'
  },
  {
    name: 'ariaLabel',
    description: 'Accessible name of the `<nav>` landmark. Default: `\'Main\'`. Give a second\nsidebar on the same page its own name, and translate it in a localised\napp — the library ships no translation layer.',
    required: false,
    type: 'string',
    default: '"Main"'
  },
  {
    name: 'collapsed',
    description: 'v-model. Whether the sidebar is collapsed. Left unset, it collapses automatically below the `sm` breakpoint.',
    required: false,
    type: 'boolean | null',
    default: 'null'
  }
]

  const sidebarSlots = [
  {
    name: 'default',
    description: 'The sidebar body — header, scroll region, footer, all composed by the app.',
    type: 'any'
  }
]

  const sidebarEmits = [
  {
    name: 'update:collapsed',
    description: 'Fired when the sidebar is collapsed or expanded.',
    type: '[value: boolean | null]'
  }
]

  const sidebarItemProps = [
  {
    name: 'label',
    description: 'Row label. Used as the accessible name and the default slot fallback.',
    required: false,
    type: 'string'
  },
  {
    name: 'accessKey',
    description: '`accesskey` attribute for a keyboard shortcut.',
    required: false,
    type: 'string'
  },
  {
    name: 'icon',
    description: 'Leading icon: a `lucide-*` class, an emoji, or a component. Any other\nstring renders nothing and warns in dev — put initials in the `#prefix`\nslot instead. Ignored when the `#prefix` slot is used.',
    required: false,
    type: 'string | Component'
  },
  {
    name: 'suffix',
    description: 'Trailing text. Ignored when the `#suffix` slot is used.',
    required: false,
    type: 'string'
  },
  {
    name: 'route',
    description: 'Navigation target. When set the row\'s main area renders as a router link;\notherwise it renders as a button. A click still invokes `onClick`.',
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
    description: 'Marks the row active (`data-state="active"`). When omitted, active state is\ninferred by matching `route` against the current route.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'onClick',
    description: 'Click handler. Bound from `@click`.',
    required: false,
    type: '((event: MouseEvent) => void)'
  }
]

  const sidebarItemSlots = [
  {
    name: 'prefix',
    description: 'Leading icon or avatar. Overrides the `icon` prop.',
    type: 'any'
  },
  {
    name: 'default',
    description: 'The label region. Overrides the `label` prop; put inline adornments here.',
    type: 'any'
  },
  {
    name: 'suffix',
    description: 'The trailing zone — a sibling of the link/button, not nested inside it. Overrides the `suffix` prop.',
    type: 'any'
  }
]

  const sidebarLabelProps = [
  {
    name: 'divider',
    description: 'When true, collapses to a horizontal divider while the sidebar is collapsed\n(matches the previous `SidebarSection` label behavior).',
    required: false,
    type: 'boolean'
  }
]

  const sidebarLabelSlots = [
  {
    name: 'default',
    description: 'The label text.',
    type: 'any'
  }
]

  const sidebarHeaderProps = [
  {
    name: 'title',
    description: 'Workspace or app title.',
    required: true,
    type: 'string'
  },
  {
    name: 'subtitle',
    description: 'Secondary line under the title, e.g. a domain or workspace slug.',
    required: false,
    type: 'string'
  },
  {
    name: 'logo',
    description: 'Leading logo: an image URL, or a component. Overridden by the `#prefix` slot.',
    required: false,
    type: 'string | Component'
  },
  {
    name: 'showLogo',
    description: 'Whether to render the leading logo/avatar box. Defaults to `true`. Set to\n`false` when workspace identity is already shown elsewhere (e.g. a left\nrail) to avoid a duplicate avatar; the title then sits flush-left. Best\npaired with a non-collapsing sidebar, since a collapsed header with no logo\nhas nothing to show.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'menuItems',
    description: 'Options rendered in the trigger\'s dropdown — the same shape `Dropdown` itself takes.',
    required: false,
    type: 'MenuOptions'
  }
]

  const sidebarHeaderSlots = [
  {
    name: 'prefix',
    description: 'Fills the default logo/initial box (a `size-7 overflow-hidden` frame — wide content clips). Falls back to the `logo` prop, then the title\'s first letter.',
    type: 'any'
  }
]

  const sidebarSectionProps = [
  {
    name: 'label',
    description: 'Section label. Renders nothing when omitted (a label-less group).',
    required: false,
    type: 'string'
  },
  {
    name: 'collapsible',
    description: 'Whether clicking the label toggles the group\'s visibility.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'collapsed',
    description: 'v-model. Whether the section is collapsed. Bind it to own the state (start a section collapsed, persist the choice); left unbound the section manages it internally, starting expanded.',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]

  const sidebarSectionSlots = [
  {
    name: 'default',
    description: 'The group\'s rows — compose `SidebarItem` (or anything else) here.',
    type: 'any'
  }
]

  const sidebarSectionEmits = [
  {
    name: 'update:collapsed',
    description: 'Fired when the section is collapsed or expanded.',
    type: '[value: boolean]'
  }
]

  const sidebarCardProps = [
  {
    name: 'title',
    description: 'Main heading text of the card. Optional when the `#title` slot is used',
    required: false,
    type: 'string'
  },
  {
    name: 'description',
    description: 'Supporting text below the title',
    required: false,
    type: 'string'
  },
  {
    name: 'theme',
    description: 'Color theme of the icon and the tinted action button; the white container never changes with theme',
    required: false,
    type: 'StatusTheme',
    default: '"gray"'
  },
  {
    name: 'icon',
    description: 'Icon next to the title: unset shows the theme\'s auto icon (gray shows the info glyph in black ink), `false` hides it, a `lucide-*` string or Component renders a custom theme-colored glyph',
    required: false,
    type: 'boolean | string | Component'
  },
  {
    name: 'action',
    description: 'The full-width tinted action button (`ButtonProps` plus `onClick({ dismiss })`)',
    required: false,
    type: 'AlertAction'
  },
  {
    name: 'dismissible',
    description: 'Shows the dismiss (×) button, which emits `dismiss`',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]

  const sidebarCardSlots = [
  {
    name: 'prefix',
    description: 'Overrides the icon area next to the title',
    type: 'any'
  },
  {
    name: 'title',
    description: 'Rich title content (overrides the `title` prop)',
    type: 'any'
  },
  {
    name: 'description',
    description: 'Rich description content (overrides the `description` prop)',
    type: 'any'
  },
  {
    name: 'actions',
    description: 'Replaces the auto-rendered action button; receives `{ dismiss }`',
    type: 'SidebarCardActionsSlotProps'
  }
]

  const sidebarCardEmits = [
  {
    name: 'dismiss',
    description: 'Fired when the user dismisses the card — the × button or the action\'s `context.dismiss()`. The parent owns hiding.',
    type: '[]'
  }
]
```
