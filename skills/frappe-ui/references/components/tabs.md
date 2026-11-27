<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Tabs/Tabs.md -->

# Tabs

Switches between panels of content, or between pages. To pick a value for a
filter, a setting or a form field, use [TabButtons](tabbuttons.md) instead.



## Anatomy

`Tabs` holds the selected value. `TabList` draws the row of triggers, and each
`TabPanel` shows while its `value` is selected. You lay the parts out and style
them directly.

```vue
<Tabs v-model="tab">
  <TabList>
    <TabTrigger value="emails" label="Emails" />
    <TabTrigger value="calls" label="Calls" />
  </TabList>
  <TabPanel value="emails">…</TabPanel>
  <TabPanel value="calls">…</TabPanel>
</Tabs>
```

## Examples

### Record sections

The default `underline` variant with a leading icon on each trigger, as on a
CRM record page.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Tabs, TabList, TabTrigger, TabPanel } from 'frappe-ui'

const tab = ref('emails')

const tabs = [
  { value: 'activity', label: 'Activity', icon: 'lucide-activity' },
  { value: 'emails', label: 'Emails', icon: 'lucide-mail' },
  { value: 'calls', label: 'Calls', icon: 'lucide-phone' },
  { value: 'tasks', label: 'Tasks', icon: 'lucide-circle-check' },
  { value: 'notes', label: 'Notes', icon: 'lucide-file-text' },
  { value: 'files', label: 'Files', icon: 'lucide-file' },
]
</script>

<template>
  <div class="w-full max-w-2xl">
    <Tabs v-model="tab">
      <TabList variant="underline">
        <TabTrigger
          v-for="t in tabs"
          :key="t.value"
          :value="t.value"
          :label="t.label"
          :icon-left="t.icon"
        />
      </TabList>
      <TabPanel v-for="t in tabs" :key="t.value" :value="t.value">
        <div class="py-4 text-base text-ink-gray-7">
          {{ t.label }} content for this record shows up here.
        </div>
      </TabPanel>
    </Tabs>
  </div>
</template>
```

### Tabs above a table

The `browser-tab` variant attaches the tabs to the panel below. The `#suffix`
slot holds a count on each tab.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Tabs, TabList, TabTrigger, TabPanel, Badge } from 'frappe-ui'

const tab = ref('deals')

const tabs = [
  { value: 'deals', label: 'Deals', count: '14' },
  { value: 'contact', label: 'Contact', count: '25' },
  { value: 'organizations', label: 'Organizations', count: '02' },
]

const deals = [
  {
    no: 1,
    organization: 'Attentive',
    returns: '0.00',
    status: 'Negotiation',
    theme: 'amber',
    email: 'bessiecooper@example.com',
  },
  {
    no: 2,
    organization: 'Gumroad',
    returns: '20,000.00',
    status: 'Ready to Close',
    theme: 'violet',
    email: 'jamescooper@example.com',
  },
  {
    no: 3,
    organization: 'Evergreen',
    returns: '10,000.00',
    status: 'Qualification',
    theme: 'blue',
    email: 'maxwell@example.com',
  },
] as const
</script>

<template>
  <div class="w-full max-w-3xl">
    <Tabs v-model="tab">
      <TabList variant="browser-tab">
        <TabTrigger
          v-for="t in tabs"
          :key="t.value"
          :value="t.value"
          :label="t.label"
        >
          <template #suffix>
            <Badge size="sm">{{ t.count }}</Badge>
          </template>
        </TabTrigger>
      </TabList>

      <!-- A fixed height keeps the page from jumping when the tab changes. -->
      <TabPanel value="deals" class="h-40">
        <table class="w-full border-collapse text-base">
          <thead>
            <tr class="text-left text-ink-gray-5">
              <th class="border-b border-outline-gray-1 p-2 font-normal">No</th>
              <th class="border-b border-outline-gray-1 p-2 font-normal">
                Organization
              </th>
              <th
                class="border-b border-outline-gray-1 p-2 text-right font-normal"
              >
                Annual Returns
              </th>
              <th class="border-b border-outline-gray-1 p-2 font-normal">
                Status
              </th>
              <th class="border-b border-outline-gray-1 p-2 font-normal">
                Email
              </th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="deal in deals" :key="deal.no" class="text-ink-gray-7">
              <td class="border-b border-outline-gray-1 p-2">{{ deal.no }}</td>
              <td class="border-b border-outline-gray-1 p-2">
                {{ deal.organization }}
              </td>
              <td class="border-b border-outline-gray-1 p-2 text-right">
                {{ deal.returns }}
              </td>
              <td class="border-b border-outline-gray-1 p-2">
                <Badge :theme="deal.theme">{{ deal.status }}</Badge>
              </td>
              <td class="border-b border-outline-gray-1 p-2">
                {{ deal.email }}
              </td>
            </tr>
          </tbody>
        </table>
      </TabPanel>
      <TabPanel value="contact" class="h-40">
        <div class="px-2 py-4 text-base text-ink-gray-7">
          Contacts show up here.
        </div>
      </TabPanel>
      <TabPanel value="organizations" class="h-40">
        <div class="px-2 py-4 text-base text-ink-gray-7">
          Organizations show up here.
        </div>
      </TabPanel>
    </Tabs>
  </div>
</template>
```

### Ticket status

The `#prefix` slot puts a status dot before each label. It receives
`{ active, disabled }`.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Tabs, TabList, TabTrigger, TabPanel } from 'frappe-ui'

const tab = ref('open')

const tabs = [
  { value: 'open', label: 'Open', dot: 'bg-surface-green-5' },
  { value: 'pending', label: 'Pending', dot: 'bg-surface-amber-5' },
  { value: 'closed', label: 'Closed', dot: 'bg-surface-gray-5' },
]
</script>

<template>
  <div class="w-full max-w-md">
    <Tabs v-model="tab">
      <TabList variant="underline">
        <TabTrigger
          v-for="t in tabs"
          :key="t.value"
          :value="t.value"
          :label="t.label"
        >
          <template #prefix>
            <span class="size-2 rounded-full" :class="t.dot" />
          </template>
        </TabTrigger>
      </TabList>
      <TabPanel v-for="t in tabs" :key="t.value" :value="t.value">
        <div class="py-4 text-base text-ink-gray-7">
          {{ t.label }} tickets show up here.
        </div>
      </TabPanel>
    </Tabs>
  </div>
</template>
```

### Icon rail

A vertical `ghost` list of icon-only triggers. A trigger with `icon` and no
default slot shows only the icon, and its `label` becomes the name screen
readers announce.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Tabs, TabList, TabTrigger, TabPanel } from 'frappe-ui'

const tab = ref('home')

const tabs = [
  { value: 'search', label: 'Search', icon: 'lucide-search' },
  { value: 'inbox', label: 'Inbox', icon: 'lucide-inbox' },
  { value: 'home', label: 'Home', icon: 'lucide-house' },
  { value: 'people', label: 'People', icon: 'lucide-users' },
  { value: 'settings', label: 'Settings', icon: 'lucide-settings' },
]
</script>

<template>
  <Tabs v-model="tab" vertical class="flex gap-4">
    <TabList variant="ghost">
      <TabTrigger
        v-for="t in tabs"
        :key="t.value"
        :value="t.value"
        :label="t.label"
        :icon="t.icon"
      />
    </TabList>
    <TabPanel v-for="t in tabs" :key="t.value" :value="t.value">
      <!-- Fixed width so the rail doesn't move when panel text changes. -->
      <div class="w-40 p-4 text-base text-ink-gray-7">{{ t.label }} panel</div>
    </TabPanel>
  </Tabs>
</template>
```

### Mailbox pages

A trigger with `route` renders as a link. Without a `v-model`, the selected
tab follows the current route. Leave out the panels and put a `<router-view>`
outside the tabs.

```vue
<script setup lang="ts">
import { onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { Tabs, TabList, TabTrigger } from 'frappe-ui'

// Without a `v-model`, selection derives from the current route. Clicking a
// trigger navigates; a `<router-view>` outside the tabs renders the page.
const route = useRoute()
const router = useRouter()

onMounted(() => router.replace('/mail/inbox'))
</script>

<template>
  <div class="w-full max-w-md">
    <Tabs>
      <TabList variant="underline">
        <TabTrigger value="inbox" label="Inbox" route="/mail/inbox" />
        <TabTrigger value="sent" label="Sent" route="/mail/sent" />
        <TabTrigger value="archive" label="Archive" route="/mail/archive" />
      </TabList>
    </Tabs>
    <div class="py-4 text-base text-ink-gray-7">
      Current route: <code>{{ route.path }}</code>
    </div>
  </div>
</template>
```

### Tabs from data

Pass a `tabs` array and the component renders every part for you. An item with
a `condition` shows only while the function returns true. The `#tab-prefix`,
`#tab-label`, `#tab-suffix` and `#tab-panel` slots shape each tab, and receive
the item as `tab`. Put your own fields in the item's `data` and read them as
`tab.data`.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Tabs, Switch } from 'frappe-ui'

const tab = ref('discussions')
const showTasks = ref(true)

const tabs = [
  {
    value: 'discussions',
    label: 'Discussions',
    data: { content: 'Discussions for this project show up here.' },
  },
  {
    value: 'pages',
    label: 'Pages',
    data: { content: 'Pages for this project show up here.' },
  },
  {
    value: 'tasks',
    label: 'Tasks',
    data: { content: 'Tasks for this project show up here.' },
    condition: () => showTasks.value,
  },
]
</script>

<template>
  <div class="flex w-full max-w-md flex-col gap-4">
    <Tabs
      v-model="tab"
      :tabs="tabs"
      variant="subtle"
      class="rounded-4 border p-2"
    >
      <template #tab-panel="{ tab: t }">
        <div class="px-2 py-4 text-base text-ink-gray-7">
          {{ t.data?.content }}
        </div>
      </template>
    </Tabs>
    <Switch v-model="showTasks" label="Show the Tasks tab" />
  </div>
</template>
```

## Behavior

### Vertical tabs

`vertical` on `Tabs` changes the orientation and the arrow keys, not the
layout. Add `class="flex"` to put the list beside the panel. With
`variant="browser-tab"`, `edge="start"` or `edge="end"` on `TabList` sets the
edge of the list the tabs attach to. `start` is the left edge in left-to-right
text. The `tabs` array mode lays itself out.

### Disabled tabs

A disabled trigger cannot be selected, and the arrow keys skip it.

### When the selected tab goes away

In `tabs` array mode, if the selected tab's `condition` turns false, the first
visible tab is selected and `update:modelValue` fires.

## API reference

```ts
const tabsProps = [
  {
    name: 'modelValue',
    description: 'Currently selected tab value.',
    required: false,
    type: 'TabValue'
  },
  {
    name: 'vertical',
    description: 'Renders tabs vertically instead of horizontally.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'dir',
    description: 'Forces layout direction; defaults to the resolved document direction.',
    required: false,
    type: '"ltr" | "rtl"'
  },
  {
    name: 'tabs',
    description: 'Shorthand mode only: items to generate triggers (and panels) from.',
    required: false,
    type: 'TabItem[]'
  },
  {
    name: 'variant',
    description: 'Shorthand mode only; forwarded to the generated TabList.',
    required: false,
    type: 'TabsVariant',
    default: '"underline"'
  },
  {
    name: 'size',
    description: 'Shorthand mode only; forwarded to the generated TabList.',
    required: false,
    type: 'TabsSize',
    default: '"sm"'
  },
  {
    name: 'edge',
    description: 'Shorthand mode only; forwarded to the generated TabList.',
    required: false,
    type: 'TabsEdge'
  }
]

  const tabsSlots = [
  {
    name: 'default',
    description: 'Composed mode: `TabList` / `TabPanel` children.',
    type: 'any'
  },
  {
    name: 'tab-prefix',
    description: 'Shorthand mode: leading content in every generated trigger.',
    type: '{ tab: TabItem; } & TabTriggerSlotProps'
  },
  {
    name: 'tab-label',
    description: 'Shorthand mode: replaces the label region of every generated trigger.',
    type: '{ tab: TabItem; } & TabTriggerSlotProps'
  },
  {
    name: 'tab-suffix',
    description: 'Shorthand mode: trailing content in every generated trigger.',
    type: '{ tab: TabItem; } & TabTriggerSlotProps'
  },
  {
    name: 'tab-panel',
    description: 'Shorthand mode: panel body for the selected tab.',
    type: '{ tab: TabItem; }'
  }
]

  const tabsEmits = [
  {
    name: 'update:modelValue',
    description: 'Fired when the selected tab changes.',
    type: '[value: TabValue]'
  }
]

  const tabListProps = [
  {
    name: 'variant',
    description: 'Visual variant.',
    required: false,
    type: 'TabsVariant',
    default: '"underline"'
  },
  {
    name: 'size',
    description: 'Size of the triggers.',
    required: false,
    type: 'TabsSize',
    default: '"sm"'
  },
  {
    name: 'edge',
    description: 'browser-tab + vertical only: the edge of the list the tabs attach to.\n`start` is the left edge in left-to-right text.',
    required: false,
    type: 'TabsEdge',
    default: '"start"'
  }
]

  const tabListSlots = [
  {
    name: 'default',
    description: '',
    type: 'any'
  }
]

  const tabTriggerProps = [
  {
    name: 'value',
    description: '',
    required: true,
    type: 'TabValue'
  },
  {
    name: 'label',
    description: 'Text shown in the label region.',
    required: false,
    type: 'string'
  },
  {
    name: 'icon',
    description: 'Icon-only trigger; `label` becomes the accessible name.',
    required: false,
    type: 'TabIcon'
  },
  {
    name: 'iconLeft',
    description: 'Leading accent icon, rendered next to the visible label.',
    required: false,
    type: 'TabIcon'
  },
  {
    name: 'disabled',
    description: '',
    required: false,
    type: 'boolean'
  },
  {
    name: 'route',
    description: 'Renders the trigger as a RouterLink. See route mode in the spec.',
    required: false,
    type: 'RouteDestination'
  }
]

  const tabTriggerSlots = [
  {
    name: 'prefix',
    description: 'Leading content, after `iconLeft`.',
    type: 'TabTriggerSlotProps'
  },
  {
    name: 'default',
    description: 'Replaces the label region.',
    type: 'TabTriggerSlotProps'
  },
  {
    name: 'suffix',
    description: 'Trailing content (badges, counts).',
    type: 'TabTriggerSlotProps'
  }
]

  const tabPanelProps = [
  {
    name: 'value',
    description: '',
    required: true,
    type: 'TabValue'
  }
]

  const tabPanelSlots = [
  {
    name: 'default',
    description: '',
    type: 'any'
  }
]
```
