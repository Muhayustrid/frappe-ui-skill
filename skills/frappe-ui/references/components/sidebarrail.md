<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/SidebarRail/SidebarRail.md -->

# SidebarRail

The narrow icon column of an app shell. For a wide column with labels, use
[`Sidebar`](sidebar.md).

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { SidebarRail, SidebarRailItem, Avatar } from 'frappe-ui'

const active = ref('design')

function logo(seed: string) {
  return `https://api.dicebear.com/9.x/shapes/svg?seed=${seed}`
}

const communities = [
  { id: 'design', label: 'Design', badge: 0, image: logo('design') },
  {
    id: 'engineering',
    label: 'Engineering',
    badge: 3,
    image: logo('engineering'),
  },
  { id: 'marketing', label: 'Marketing', badge: 0, image: logo('marketing') },
  { id: 'sales', label: 'Sales', badge: 0, image: null },
]
</script>

<template>
  <div
    class="flex h-[420px] w-fit overflow-hidden rounded-5 border bg-surface-base"
  >
    <SidebarRail>
      <SidebarRailItem
        label="Home"
        variant="ghost"
        icon="lucide-house"
        @click="active = ''"
      />

      <!-- The consumer lays the middle out; flex-1 pushes the rest to the bottom. -->
      <div class="flex w-full flex-1 flex-col items-center gap-3 pt-3">
        <SidebarRailItem
          v-for="c in communities"
          :key="c.id"
          :label="c.label"
          :active="active === c.id"
          :badge="c.badge"
          badge-style="dot"
          @click="active = c.id"
        >
          <!-- The item paints no fill under an image. Initials sit on the
               item's own fill. -->
          <img
            v-if="c.image"
            :src="c.image"
            alt=""
            class="size-7 rounded-[7px]"
          />
          <span v-else class="text-2xs-medium uppercase text-ink-gray-5">
            {{ c.label.slice(0, 2) }}
          </span>
        </SidebarRailItem>
      </div>

      <SidebarRailItem label="Search" variant="ghost" icon="lucide-search" />
      <SidebarRailItem
        label="Notifications"
        variant="ghost"
        icon="lucide-bell"
        :badge="5"
      />
      <SidebarRailItem label="You" variant="ghost" class="mt-1">
        <Avatar
          label="Jane Doe"
          image="https://api.dicebear.com/9.x/notionists/svg?seed=Jane&backgroundColor=c0aede"
          size="md"
        />
      </SidebarRailItem>
    </SidebarRail>
  </div>
</template>
```

## Anatomy

`SidebarRail` is a fixed 50px column with one slot. `SidebarRailItem` is one
cell in it, with a tooltip. The rail has no layout slots and does not scroll,
so lay out the children with flex classes. Give the middle group `flex-1` to
push the items above and below it to the ends.

```vue
<SidebarRail>
  <SidebarRailItem label="Home" variant="ghost" icon="lucide-house" route="/" />

  <div class="flex w-full flex-1 flex-col items-center gap-3 overflow-y-auto pt-3">
    <SidebarRailItem label="Design" route="/c/design" :badge="3">
      <span>DE</span>
    </SidebarRailItem>
  </div>

  <SidebarRailItem label="Search" variant="ghost" icon="lucide-search" />
  <SidebarRailItem label="You" variant="ghost">
    <Avatar label="Jane Doe" size="md" />
  </SidebarRailItem>
</SidebarRail>
```

## Behavior

### Rail and sidebar

`SidebarRail` shares the `Sidebar` name because the two sit side by side in the
same app frame, not because one contains the other. Use a rail with a `Sidebar`
beside it, a rail on its own, or a sidebar on its own. The rail is not a
collapsed `Sidebar`. `Sidebar` collapses by itself.

### Layout and scrolling

Put fixed items, such as a logo or a user menu, directly in the rail. If the
middle list can overflow, wrap it in your own `overflow-y-auto` container.

### Tooltips

Each item shows its `label` in a tooltip that opens away from the navigation:
to the right, or to the left in a `DesktopShell` with `navSide="right"`. `description` adds a
second line, such as "12 members". The rail shares one tooltip context across
its items, so moving between items shows each tooltip with no delay.

### Variants

- `variant="subtle"` (default) is a filled cell with a bar on its outer edge (left,
  or right when the shell's `navSide` is `right`) when active. Use the default slot for an image, an avatar or initials. When the
  slot holds an `<img>`, the cell paints no fill, so the image's rounded corners
  show no gray rim. The bar still marks the active item.
- `variant="ghost"` is transparent until hovered, and raised when active. Pass
  an `icon` for a shortcut such as Search or Notifications.

### Links

`route` renders a router link and `href` a plain same-tab link. `route` wins
when both are set. A string `route` falls back to a plain link when no router
is installed. An item with neither is a button. All three emit `click`.

When `active` is unset, a routed item is active when its `route` matches the
current route.

### Unread badge

`badge` takes an unread count. `badgeStyle="count"` (default) shows it as a
pill, and counts above 99 show as "99+". `badgeStyle="dot"` shows a dot, and
the tooltip spells out the number unless `description` is set. The pill is
rendered outside the rail, so an `overflow-hidden` container does not clip it.

### Attributes

Attributes on `SidebarRailItem` go on the clickable cell (the `<button>` or the
link), not on the tooltip wrapper. A `class` adds to the cell's own classes.

## Accessibility

Each item uses its `label` as its accessible name. When `badge` is above zero,
the count is added to the name, for example "Notifications, 3 unread". The
active item gets `aria-current="page"`.

## API reference

```ts
const sidebarRailSlots = [
  {
    name: 'default',
    description: '`SidebarRailItem`s, and any fixed anchors (a logo, a user menu). Layout is plain flex — no built-in scrolling.',
    type: 'any'
  }
]

  const sidebarRailItemProps = [
  {
    name: 'label',
    description: 'Tooltip text and the base of the item\'s accessible label.',
    required: true,
    type: 'string'
  },
  {
    name: 'description',
    description: 'Second line in the tooltip, under the label — e.g. "12 members". Overrides the\nunread count a `dot` badge would otherwise spell out there.',
    required: false,
    type: 'string'
  },
  {
    name: 'icon',
    description: 'Icon CSS class, e.g. `lucide-search`, or a component. Rendered centered in the cell.\nIgnored when the default slot is used (for an image, avatar, or initials).',
    required: false,
    type: 'string | Component'
  },
  {
    name: 'route',
    description: 'Navigation target. When set, the item renders as a router link; otherwise\nit renders as a button. A `click` event fires in both cases.',
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
    description: 'Marks the item as the current destination (indicator bar / active fill).',
    required: false,
    type: 'boolean'
  },
  {
    name: 'badge',
    description: 'Unread count. Drives the badge and folds into the accessible label.',
    required: false,
    type: 'number',
    default: '0'
  },
  {
    name: 'badgeStyle',
    description: 'How the badge renders: a count pill or a small dot.',
    required: false,
    type: '"count" | "dot"',
    default: '"count"'
  },
  {
    name: 'variant',
    description: 'Visual treatment.\n- `subtle` (default): a filled cell with a left indicator bar when active —\n  for image/avatar items like communities or workspaces. The cell paints\n  no fill when its default slot holds an `<img>`.\n- `ghost`: transparent until hovered, a raised highlight when active — for\n  icon shortcuts like Search or Notifications.',
    required: false,
    type: '"subtle" | "ghost"',
    default: '"subtle"'
  }
]

  const sidebarRailItemSlots = [
  {
    name: 'default',
    description: 'Custom content in place of the default icon — an image, avatar, or initials.',
    type: 'any'
  }
]

  const sidebarRailItemEmits = [
  {
    name: 'click',
    description: '',
    type: '[event: MouseEvent]'
  }
]
```
