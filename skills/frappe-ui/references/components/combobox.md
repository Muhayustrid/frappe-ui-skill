<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Combobox/Combobox.md -->

# Combobox

A searchable list that picks one option. For a short list with no search, use
[Select](select.md), and to pick several values, use
[MultiSelect](multiselect.md).



## Examples

### Assign a member

`#item-prefix` and `#item-label` show an avatar and an email on each row, and
`#prefix` shows the selected member's avatar. A `type: 'custom'` row at the end
always offers an invite action.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { Avatar, Combobox } from 'frappe-ui'

type Member = {
  label: string
  value: string
  email: string
  image: string
  role: string
}

const value = ref<string>('')
const lastAction = ref<string>('')

// Using pravatar.cc for stable, realistic avatar photos keyed by email.
const members: Member[] = [
  {
    label: 'Alex Rivera',
    value: 'alex@example.com',
    email: 'alex@example.com',
    image: 'https://i.pravatar.cc/80?u=alex@example.com',
    role: 'Engineering',
  },
  {
    label: 'Priya Shah',
    value: 'priya@example.com',
    email: 'priya@example.com',
    image: 'https://i.pravatar.cc/80?u=priya@example.com',
    role: 'Design',
  },
  {
    label: 'Marcus Lee',
    value: 'marcus@example.com',
    email: 'marcus@example.com',
    image: 'https://i.pravatar.cc/80?u=marcus@example.com',
    role: 'Product',
  },
  {
    label: 'Sofia Hartmann',
    value: 'sofia@example.com',
    email: 'sofia@example.com',
    image: 'https://i.pravatar.cc/80?u=sofia@example.com',
    role: 'Engineering',
  },
  {
    label: 'Kenji Tanaka',
    value: 'kenji@example.com',
    email: 'kenji@example.com',
    image: 'https://i.pravatar.cc/80?u=kenji@example.com',
    role: 'Design',
  },
]

// Members appear as regular selectable options. The invite row is a custom
// action with `condition: () => true` so the picker always offers it,
// regardless of the current query.
const options = [
  ...members,
  {
    type: 'custom' as const,
    key: 'invite',
    label: 'Invite new member',
    slot: 'invite',
    condition: () => true,
    onClick: ({ query }: { query: string }) => {
      lastAction.value = query ? `Invited "${query}"` : 'Opened invite dialog'
    },
  },
]

const selected = computed(
  () => members.find((m) => m.value === value.value) ?? null,
)
</script>

<template>
  <div class="grid gap-3">
    <Combobox
      v-model="value"
      :options="options"
      placeholder="Assign to…"
      open-on-focus
      class="w-80"
    >
      <template #prefix>
        <Avatar v-if="selected" :image="selected.image" size="sm" />
      </template>

      <template #item-prefix="{ item }">
        <Avatar
          v-if="item.type !== 'custom'"
          :image="(item as Member).image"
          :label="item.label"
          size="sm"
        />
        <div
          v-else
          class="flex size-6 items-center justify-center rounded-full bg-surface-gray-2 text-ink-gray-6"
        >
          <span class="lucide-user-plus size-3.5" />
        </div>
      </template>

      <template #item-label="{ item }">
        <div v-if="item.type !== 'custom'" class="min-w-0">
          <div class="truncate">{{ item.label }}</div>
          <div class="truncate text-p-sm text-ink-gray-5">
            {{ (item as Member).email }}
          </div>
        </div>
      </template>

      <template #item-invite="{ query }">
        <span class="truncate">
          {{ query ? `Invite "${query}"` : 'Invite new member' }}
        </span>
      </template>
    </Combobox>

    <div class="text-sm text-ink-gray-5">
      {{
        selected
          ? `Assigned to ${selected.label}`
          : lastAction || 'No one assigned'
      }}
    </div>
  </div>
</template>
```

### Status picker

`#item-label` puts a colored dot beside a two-line label, lined up with the
first line.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { Combobox } from 'frappe-ui'

type StatusOption = {
  label: string
  value: string
  color: string
  description: string
}

const value = ref<string>('in-progress')

const statuses: StatusOption[] = [
  {
    label: 'Backlog',
    value: 'backlog',
    color: 'bg-gray-400',
    description: 'Ideas and future work',
  },
  {
    label: 'Todo',
    value: 'todo',
    color: 'bg-gray-500',
    description: 'Ready to be picked up',
  },
  {
    label: 'In Progress',
    value: 'in-progress',
    color: 'bg-blue-500',
    description: 'Actively being worked on',
  },
  {
    label: 'In Review',
    value: 'in-review',
    color: 'bg-yellow-500',
    description: 'Awaiting feedback',
  },
  {
    label: 'Done',
    value: 'done',
    color: 'bg-green-500',
    description: 'Shipped and verified',
  },
  {
    label: 'Cancelled',
    value: 'cancelled',
    color: 'bg-gray-300',
    description: 'Will not be worked on',
  },
]

const selected = computed(
  () => statuses.find((s) => s.value === value.value) ?? null,
)
</script>

<template>
  <div class="grid gap-3">
    <Combobox
      v-model="value"
      :options="statuses"
      placeholder="Set status"
      open-on-focus
      class="w-72"
    >
      <template #prefix>
        <span
          v-if="selected"
          :class="['size-2 rounded-full', selected.color]"
          aria-hidden="true"
        />
      </template>

      <!--
        The dot is rendered inside the label region so it aligns with the
        first line of text (not the vertical center of a two-line row).
      -->
      <template #item-label="{ item }">
        <div class="flex items-start gap-2">
          <span
            :class="[
              'mt-[4px] size-2 shrink-0 rounded-full',
              (item as StatusOption).color,
            ]"
            aria-hidden="true"
          />
          <div class="min-w-0">
            <div class="truncate">{{ item.label }}</div>
            <div class="truncate text-p-sm text-ink-gray-5">
              {{ (item as StatusOption).description }}
            </div>
          </div>
        </div>
      </template>
    </Combobox>
  </div>
</template>
```

### Reaction picker

`trigger="button"` renders a button, and the search input moves into the top
of the popover. The button shows the selected option's label and icon. The
`#prefix` slot shows an icon while nothing is picked.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Combobox } from 'frappe-ui'

const value = ref<string>('')

const emojis = [
  {
    group: 'Smileys',
    options: [
      { label: 'Grinning', value: 'grinning', icon: '😀' },
      { label: 'Laughing', value: 'laughing', icon: '😂' },
      { label: 'Heart Eyes', value: 'heart-eyes', icon: '😍' },
      { label: 'Thinking', value: 'thinking', icon: '🤔' },
      { label: 'Mind Blown', value: 'mind-blown', icon: '🤯' },
    ],
  },
  {
    group: 'Gestures',
    options: [
      { label: 'Thumbs Up', value: 'thumbs-up', icon: '👍' },
      { label: 'Clap', value: 'clap', icon: '👏' },
      { label: 'Party', value: 'party', icon: '🎉' },
      { label: 'Rocket', value: 'rocket', icon: '🚀' },
      { label: 'Fire', value: 'fire', icon: '🔥' },
    ],
  },
  {
    group: 'Objects',
    options: [
      { label: 'Sparkles', value: 'sparkles', icon: '✨' },
      { label: 'Bulb', value: 'bulb', icon: '💡' },
      { label: 'Warning', value: 'warning', icon: '⚠️' },
      { label: 'Check', value: 'check', icon: '✅' },
      { label: 'Cross', value: 'cross', icon: '❌' },
    ],
  },
]
</script>

<template>
  <Combobox
    v-model="value"
    trigger="button"
    :options="emojis"
    placeholder="Pick a reaction"
  >
    <template #prefix>
      <span class="lucide-smile size-4 text-ink-gray-6" />
    </template>
  </Combobox>
</template>
```

### Search people on the server

Bind `v-model:query`, fetch as the query changes, and pass the results to
`options`. See [Server search](#server-search) for the four things to handle.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { useDebounceFn } from '@vueuse/core'
import { Avatar, Combobox } from 'frappe-ui'

type Member = {
  label: string
  value: string
  image: string
  role: string
}

const ALL_MEMBERS: Member[] = [
  {
    label: 'Alex Rivera',
    value: 'alex@example.com',
    image: 'https://i.pravatar.cc/80?u=alex@example.com',
    role: 'Engineering',
  },
  {
    label: 'Alexandra Chen',
    value: 'alexandra@example.com',
    image: 'https://i.pravatar.cc/80?u=alexandra@example.com',
    role: 'Design',
  },
  {
    label: 'Priya Shah',
    value: 'priya@example.com',
    image: 'https://i.pravatar.cc/80?u=priya@example.com',
    role: 'Design',
  },
  {
    label: 'Priyanka Mehta',
    value: 'priyanka@example.com',
    image: 'https://i.pravatar.cc/80?u=priyanka@example.com',
    role: 'Product',
  },
  {
    label: 'Marcus Lee',
    value: 'marcus@example.com',
    image: 'https://i.pravatar.cc/80?u=marcus@example.com',
    role: 'Product',
  },
  {
    label: 'Maria Garcia',
    value: 'maria@example.com',
    image: 'https://i.pravatar.cc/80?u=maria@example.com',
    role: 'Marketing',
  },
  {
    label: 'Sofia Hartmann',
    value: 'sofia@example.com',
    image: 'https://i.pravatar.cc/80?u=sofia@example.com',
    role: 'Engineering',
  },
  {
    label: 'Kenji Tanaka',
    value: 'kenji@example.com',
    image: 'https://i.pravatar.cc/80?u=kenji@example.com',
    role: 'Design',
  },
  {
    label: 'Nadia Okafor',
    value: 'nadia@example.com',
    image: 'https://i.pravatar.cc/80?u=nadia@example.com',
    role: 'Product',
  },
  {
    label: 'Diego Alvarez',
    value: 'diego@example.com',
    image: 'https://i.pravatar.cc/80?u=diego@example.com',
    role: 'Engineering',
  },
]

// Mocks a server endpoint: 400ms latency + substring match on label/value.
// A real backend would rank and fuzzy-match, which is why the picker below
// passes `:filterable="false"` — a second client-side substring pass would
// silently drop anything the server matched but the query doesn't contain.
function searchMembersApi(query: string): Promise<Member[]> {
  return new Promise((resolve) => {
    setTimeout(() => {
      const q = query.trim().toLowerCase()
      const matches = q
        ? ALL_MEMBERS.filter(
            (m) =>
              m.label.toLowerCase().includes(q) ||
              m.value.toLowerCase().includes(q),
          )
        : ALL_MEMBERS
      resolve(matches.slice(0, 6))
    }, 400)
  })
}

const value = ref<string>('')
const query = ref('')
const results = ref<Member[]>([])
const loading = ref(false)
const knownById = ref(new Map<string, Member>())

let requestId = 0
async function fetchMembers(next: string) {
  const id = ++requestId
  loading.value = true
  const members = await searchMembersApi(next)
  // Drop stale responses so an earlier-but-slower request can't overwrite
  // the latest results.
  if (id !== requestId) return
  results.value = members
  for (const m of members) knownById.value.set(m.value, m)
  loading.value = false
}

const onQueryChange = useDebounceFn(fetchMembers, 250)

// Merge the selected member into the options so the trigger stays
// resolvable after the query narrows the result set.
const options = computed<Member[]>(() => {
  const byId = new Map<string, Member>()
  for (const m of results.value) byId.set(m.value, m)
  if (value.value && !byId.has(value.value)) {
    const existing = knownById.value.get(value.value)
    if (existing) byId.set(value.value, existing)
  }
  return Array.from(byId.values())
})

function onOpen(isOpen: boolean) {
  if (!isOpen) return
  // Binding `query` — or merely listening for `@update:query` — hands
  // ownership over, so the open-time reset the combobox does for an unbound
  // query is ours to do. Without it the committed label stays in the search
  // box and the next keystroke appends to it.
  query.value = ''
  if (results.value.length === 0) fetchMembers('')
}
</script>

<template>
  <!-- The `as` casts on the two handlers below exist because these model
       emits are declared twice; drop them when #1096 removes the duplicates. -->
  <Combobox
    v-model="value"
    v-model:query="query"
    :options="options"
    :loading="loading"
    :filterable="false"
    trigger="button"
    placeholder="Search members…"
    empty-text="No members found"
    class="w-80"
    @update:query="(q) => onQueryChange(q as string)"
    @update:open="(isOpen) => onOpen(isOpen as boolean)"
  >
    <template #item-prefix="{ item }">
      <Avatar :image="(item as Member).image" :label="item.label" size="sm" />
    </template>

    <template #item-label="{ item }">
      <div class="min-w-0 flex justify-between">
        <div class="truncate">{{ item.label }}</div>
        <div class="truncate text-p-sm text-ink-gray-5">
          {{ (item as Member).role }}
        </div>
      </div>
    </template>
  </Combobox>
</template>
```

### Create a tag

A `type: 'custom'` row creates a tag from the typed text and selects it.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { Combobox } from 'frappe-ui'

const value = ref<string>('')
const tags = ref<string[]>(['bug', 'enhancement', 'docs', 'discussion'])

const selectableOptions = computed(() =>
  tags.value.map((t) => ({ label: t, value: t })),
)

// "Create new" is just a `type: 'custom'` row. `condition` is authoritative
// — it runs even before the user types, so the row can decide for itself
// when to appear based on the typed query and current selection.
const options = computed(() => [
  ...selectableOptions.value,
  {
    type: 'custom' as const,
    key: 'create',
    label: 'Create tag',
    slot: 'create',
    keepOpen: false,
    condition: ({ query }: { query: string }) => {
      const q = query.trim().toLowerCase()
      if (!q) return false
      if (q === value.value?.toLowerCase()) return false
      return !tags.value.some((t) => t.toLowerCase() === q)
    },
    onClick: ({ query }: { query: string }) => {
      const next = query.trim()
      if (!next) return
      tags.value = [...tags.value, next]
      value.value = next
    },
  },
])

function getBgClass(item: { label: string }) {
  const palette = [
    'bg-surface-amber-3',
    'bg-surface-blue-3',
    'bg-surface-green-3',
    'bg-surface-gray-3',
  ]
  const hash = item.label
    .toLowerCase()
    .split('')
    .reduce((a, b) => a + b.charCodeAt(0), 0)
  return palette[hash % palette.length]
}
</script>

<template>
  <div class="grid gap-3 shrink-0">
    <Combobox
      v-model="value"
      :options="options"
      placeholder="Search or create a tag"
      open-on-focus
      class="w-64"
    >
      <template #item-prefix="{ item }">
        <span
          v-if="item.key !== 'create'"
          :class="getBgClass(item)"
          class="size-3 rounded-1"
        />
        <span
          v-if="item.key === 'create'"
          class="rounded-1 bg-surface-gray-8 lucide-tag"
        />
      </template>
      <template #item-create="{ query }">
        <div class="flex">
          <span class="truncate">
            Create
            <span v-if="query" class="font-medium text-ink-gray-8">
              {{ query }}
            </span>
          </span>
        </div>
      </template>
    </Combobox>

    <div class="text-sm text-ink-gray-5">
      Selected: <code class="text-ink-gray-7">{{ value || 'None' }}</code>
    </div>
    <div class="text-sm text-ink-gray-5">
      Tags: <code class="text-ink-gray-7">{{ tags.join(', ') }}</code>
    </div>
  </div>
</template>
```

### Country picker with a footer

The `#footer` slot sits below the options and stays in place while the list
scrolls. Here it shows the query and a Clear button.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Combobox } from 'frappe-ui'

const value = ref('')

// A long list so the viewport scrolls — the footer should stay pinned to the
// bottom of the popover instead of scrolling away with the items (#717).
const countries = [
  'Argentina',
  'Australia',
  'Brazil',
  'Canada',
  'Denmark',
  'Egypt',
  'France',
  'Germany',
  'India',
  'Indonesia',
  'Japan',
  'Kenya',
  'Mexico',
  'Netherlands',
  'Norway',
  'Portugal',
  'Singapore',
  'Spain',
  'Sweden',
  'United Kingdom',
  'United States',
  'Vietnam',
]
</script>

<template>
  <div class="grid gap-3">
    <Combobox
      v-model="value"
      :options="countries"
      placeholder="Pick a country"
      open-on-focus
      class="w-64"
    >
      <template #footer="{ query, selectedOption, clear, setOpen }">
        <div
          class="flex items-center justify-between border-t border-outline-gray-1 px-3 py-2 text-sm text-ink-gray-5"
        >
          <span v-if="query">
            Searching “<span class="text-ink-gray-7">{{ query }}</span
            >”
          </span>
          <span v-else>{{ countries.length }} countries</span>
          <button
            v-if="selectedOption"
            class="text-ink-gray-7 hover:text-ink-gray-8"
            @click="clear"
          >
            Clear
          </button>
          <button
            v-else
            class="text-ink-gray-7 hover:text-ink-gray-8"
            @click="setOpen(false)"
          >
            Close
          </button>
        </div>
      </template>
    </Combobox>

    <div class="text-sm text-ink-gray-5">
      Selected: <code class="text-ink-gray-7">{{ value || 'None' }}</code>
    </div>
  </div>
</template>
```

### In a dialog

Comboboxes in a `Dialog` need no extra setup. When the popover closes, focus
goes back to the trigger, inside the dialog.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button, Combobox, Dialog } from 'frappe-ui'

const open = ref(false)
const repo = ref('frappe-ui')
const reaction = ref('')

const repos = [
  'gameplan',
  'frappe-ui',
  'frappe',
  'erpnext',
  'helpdesk',
  'crm',
  'wiki',
  'insights',
]

const emojis = [
  {
    group: 'Smileys',
    options: [
      { label: 'Grinning', value: 'grinning', icon: '😀' },
      { label: 'Laughing', value: 'laughing', icon: '😂' },
      { label: 'Heart Eyes', value: 'heart-eyes', icon: '😍' },
      { label: 'Thinking', value: 'thinking', icon: '🤔' },
    ],
  },
  {
    group: 'Gestures',
    options: [
      { label: 'Thumbs Up', value: 'thumbs-up', icon: '👍' },
      { label: 'Party', value: 'party', icon: '🎉' },
      { label: 'Fire', value: 'fire', icon: '🔥' },
    ],
  },
]
</script>

<template>
  <Button @click="open = true">Open dialog</Button>

  <Dialog v-model="open">
    <template #title>
      <h3 class="text-3xl-semibold text-ink-gray-9">
        Combobox inside Dialog
      </h3>
    </template>

    <template #default>
      <div class="space-y-4">
        <div class="flex flex-col gap-1">
          <label class="text-sm text-ink-gray-7">Repository</label>
          <Combobox
            v-model="repo"
            :options="repos"
            placeholder="Pick a repo"
            open-on-focus
          />
        </div>

        <div class="flex flex-col gap-1">
          <label class="text-sm text-ink-gray-7">Reaction</label>
          <Combobox
            v-model="reaction"
            trigger="button"
            :options="emojis"
            placeholder="Pick a reaction"
          >
            <template #prefix>
              <span class="lucide-smile size-4 text-ink-gray-6" />
            </template>
          </Combobox>
        </div>

        <div class="rounded-4 bg-surface-gray-1 p-3 text-sm text-ink-gray-7">
          <div>
            Repo: <code>{{ repo || 'None' }}</code>
          </div>
          <div>
            Reaction: <code>{{ reaction || 'None' }}</code>
          </div>
        </div>
      </div>
    </template>

    <template #actions="{ close }">
      <Button variant="solid" @click="close">Done</Button>
    </template>
  </Dialog>
</template>
```

## Behavior

### Options

`options` takes strings, `{ label, value }` objects, or groups of
`{ group, options }`. A group label shows above its options.

### Custom rows

A `type: 'custom'` option is a row that runs an action instead of setting a
value. Its `condition` callback decides when the row shows, and `onClick`
receives the typed `query`. `slot: 'create'` renders the row with the
`#item-create` slot.

There is no "create new" prop, because what "create" means changes from app to
app. Make a custom row: use `condition` to hide it when the query is empty or
already matches an option, and use `onClick` to save the new value and set the
model. `Enter` picks the row when it is highlighted, so typing a new name and
pressing `Enter` creates it.

### Free text

A value that matches no option is kept. The trigger shows the raw string. A
custom row that commits the query therefore turns `Combobox` into a text input
with suggestions.

### Server search

To load options from a server:

1. Pass `:filterable="false"`, so the list does not filter again what the
   server already matched.
2. Drop old responses, for example with a request id, so a slow earlier
   request cannot overwrite newer results.
3. Keep the selected option in `options`, so the trigger can still show its
   label after the results change.
4. Clear the query yourself when the popover opens. See
   [Search query](#search-query).

`loading` shows a loading state in place of the results. A custom row's
`condition` still runs when `filterable` is `false`, because it decides
visibility, not filtering.

### Search query

`v-model:query` is optional. Listening for `@update:query` counts as binding
it: there is no way to only watch the query. Once you bind it, the combobox
stops clearing the search box, so the selected label stays in it and the next
key press adds to it. If you listen, also bind `v-model:query` and clear it on
`@update:open`.

To read the query without taking control of it, use `#search-prefix`,
`#search-suffix` or `#footer`. Each receives the query.

In `trigger="input"` mode the input also shows the selected value. When the
query is not bound, it follows the selected option's label.

### Search row

In `trigger="button"` mode the search input sits at the top of the popover.
`#search-prefix` and `#search-suffix` add content to that row. Both receive
`{ query, setQuery, disabled, focus }`.

`hideSearch` removes the row, which suits short fixed lists. The `#search-*`
slots go with it. In `trigger="input"` mode there is no row in the popover,
because the trigger is the search input, so `hideSearch` does nothing.

### Item slots

`#item-prefix`, `#item-label` and `#item-suffix` change parts of the standard
row. `#item` replaces the whole row, including its outer element.

In `trigger="button"` mode, `#item-prefix` also renders the selected option's
prefix on the button. `#prefix` shows before anything is picked.

### Trigger slots

`#trigger`, `#prefix`, `#suffix` and `#footer` receive `open`, `disabled`,
`setOpen` and `close`, together with the selection fields. `close()` is the
same as `setOpen(false)`. `#suffix` replaces the chevron.

### Clear button

To add a clear button, put it in `#suffix` or `#trigger`. Use `@click.stop`
so the click does not toggle the popover, and `@pointerdown.stop` so the
trigger does not take the press.

### Label, description and error

`label` renders above the field and `description` below it. `error` renders
below the field and hides `description`. It takes a string, an array of strings
(one line each), or an `Error`, the same values as
[ErrorMessage](errormessage.md). An empty string or an empty array means no
error. `required` adds a red asterisk to the label.

The `#label` slot replaces the label text and the required marker, and receives
`{ required }`. A `#description` slot is not hidden by `error`. It renders
above the error.

## Accessibility

While `error` is set, the input gets `aria-invalid` and an `aria-errormessage`
that points to the error text.

## API reference

```ts
const propsData = [
  {
    name: 'modelValue',
    description: 'Committed value. `null` when nothing is selected.',
    required: false,
    type: 'ComboboxOptionValue | null'
  },
  {
    name: 'options',
    description: 'Options rendered in the popover.',
    required: false,
    type: 'ComboboxOption[]',
    default: '[]'
  },
  {
    name: 'trigger',
    description: 'Shape of the trigger.\n- `\'input\'` (default): user types directly into the trigger\n- `\'button\'`: render a button trigger; search input moves into the\n  popover header. Label + prefix auto-derive from the selected option.',
    required: false,
    type: '"input" | "button"',
    default: '"input"'
  },
  {
    name: 'variant',
    description: 'Visual style of the combobox.',
    required: false,
    type: 'InputVariant',
    default: '"subtle"'
  },
  {
    name: 'size',
    description: 'Size of the trigger and option rows.',
    required: false,
    type: 'InputSize',
    default: '"sm"'
  },
  {
    name: 'placeholder',
    description: 'Placeholder text shown when no value is selected.',
    required: false,
    type: 'string',
    default: '"Select option"'
  },
  {
    name: 'disabled',
    description: 'Disables the combobox.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'open',
    description: 'Controls the popover visibility.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'query',
    description: 'Controls the search query. Optional — the combobox owns it otherwise.\n\nWhen it is bound the consumer owns the query: the combobox never resets it\non its own — not on open, not on close, not on mount, not on `clear()`. It\nstill follows the committed option\'s label in `trigger="input"` mode, where\nthe input is the value display rather than a filter. Unbound,\n`trigger="button"` mode still clears the search box every time the popover\nopens.',
    required: false,
    type: 'string',
    default: '""'
  },
  {
    name: 'openOnFocus',
    description: 'Opens the popover when the input receives focus.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'openOnClick',
    description: 'Opens the popover when the input is clicked.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'side',
    description: 'Preferred popover side.',
    required: false,
    type: 'PopoverSide',
    default: '"bottom"'
  },
  {
    name: 'align',
    description: 'Preferred popover alignment.',
    required: false,
    type: 'PopoverAlign',
    default: '"start"'
  },
  {
    name: 'offset',
    description: 'Gap between trigger and content.',
    required: false,
    type: 'number',
    default: '4'
  },
  {
    name: 'portalTo',
    description: 'Teleport target for the popover content. Unset, an embedding host\'s target is used, else `body`.',
    required: false,
    type: 'PortalTarget'
  },
  {
    name: 'loading',
    description: 'Replaces the results with a loading state.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'emptyText',
    description: 'Fallback empty-state copy.',
    required: false,
    type: 'string',
    default: '"No results"'
  },
  {
    name: 'hideSearch',
    description: 'Hides the in-popover search row (button mode only — in input mode the\ntrigger *is* the search input).\n\nThe `#search-prefix` / `#search-suffix` slots live inside that row and\nare not rendered when this is `true`.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'filterable',
    description: 'Client-side substring filtering of `options` as the user types.\n\nSet to `false` for pickers whose options come from a server search: the\nbackend already decided what matches, and a second literal substring\npass on the client silently drops fuzzy, ranked, or id-based results.\n\nA custom option\'s `condition` callback is consumer-declared visibility\nrather than client filtering, so it keeps running either way.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'label',
    description: 'Label rendered above (or beside, for binary controls) the input.',
    required: false,
    type: 'string'
  },
  {
    name: 'description',
    description: 'Helper text rendered below the input.\nHidden when `error` is set. A `#description` slot is not: it renders\nbeside the error, and is referenced alongside it.',
    required: false,
    type: 'string'
  },
  {
    name: 'error',
    description: 'Error message rendered below the input. When set, the control receives\n`aria-invalid="true"` and `data-state="invalid"`. Takes a string, an\narray of strings, or an `Error` whose `messages` are rendered as stacked\nlines (with `Error.message` as the fallback). This is the same value\n`ErrorMessage.message` takes. An empty array and an empty string both\nmean no error.',
    required: false,
    type: 'ErrorMessageValue'
  },
  {
    name: 'required',
    description: 'Marks the field as required. Renders an asterisk next to the label, with\n`sr-only` text that announces it, and forwards `required` /\n`aria-required` to the underlying control where the control\'s role allows\nit. `data-required` is set either way.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'id',
    description: 'HTML id of the underlying control. Auto-generated via `useId()` if omitted.',
    required: false,
    type: 'string'
  }
]

  const slotsData = [
  {
    name: 'trigger',
    description: 'Fully custom trigger renderer.',
    type: 'ComboboxControlSlotProps'
  },
  {
    name: 'label',
    description: 'Overrides the rendered label content. Receives `{ required }`.',
    type: '{ required: boolean; }'
  },
  {
    name: 'description',
    description: 'Overrides the rendered description content.',
    type: 'any'
  },
  {
    name: 'prefix',
    description: 'Content rendered before the default input. Receives the same shape\nas the other control slots.',
    type: 'ComboboxControlSlotProps'
  },
  {
    name: 'suffix',
    description: 'Content rendered after the input (input mode) or label (button mode).\nProviding this slot **replaces the default chevron** — render your\nown fallback (e.g. the chevron) when your slot content is conditional.\nCommon use: an inline clear button. Use `@click.stop` and\n`@pointerdown.stop` so the press doesn\'t toggle the popover.',
    type: 'ComboboxControlSlotProps'
  },
  {
    name: 'search-prefix',
    description: 'Content rendered before the in-popover search input (button mode only).\nNot rendered when `hideSearch` is set.',
    type: 'ComboboxSearchSlotProps'
  },
  {
    name: 'search-suffix',
    description: 'Content rendered after the in-popover search input (button mode only).\nNot rendered when `hideSearch` is set.',
    type: 'ComboboxSearchSlotProps'
  },
  {
    name: 'item',
    description: 'Replaces the entire row.',
    type: 'ComboboxItemSlotProps'
  },
  {
    name: 'group-label',
    description: 'Custom renderer for group labels.',
    type: 'ComboboxGroupLabelSlotProps'
  },
  {
    name: 'empty',
    description: 'Fallback content rendered when there are no results.',
    type: 'ComboboxEmptySlotProps'
  },
  {
    name: 'footer',
    description: 'Content rendered after the list. Stays pinned below the scrollable\noptions.',
    type: 'ComboboxControlSlotProps'
  },
  {
    name: 'item-prefix',
    description: 'Shared content rendered before the standard row label.',
    type: 'ComboboxItemSlotProps'
  },
  {
    name: 'item-label',
    description: 'Shared content rendered for the standard row label area.',
    type: 'ComboboxItemSlotProps'
  },
  {
    name: 'item-suffix',
    description: 'Shared content rendered after the standard row label area.',
    type: 'ComboboxItemSlotProps'
  }
]

  const emitsData = [
  {
    name: 'update:open',
    description: 'Fired when the open state changes.',
    type: '[value: boolean]'
  },
  {
    name: 'blur',
    description: 'Fired when the input loses focus.',
    type: '[event: FocusEvent]'
  },
  {
    name: 'focus',
    description: 'Fired when the input receives focus.',
    type: '[event: FocusEvent]'
  },
  {
    name: 'update:modelValue',
    description: 'Fired when the model value changes.',
    type: '[value: ComboboxOptionValue | null | undefined]'
  },
  {
    name: 'update:query',
    description: 'Fired when the query changes.',
    type: '[value: string]'
  },
  {
    name: 'update:selectedOption',
    description: 'Fired when the resolved selected option changes.',
    type: '[option: ComboboxSelectableOption | ComboboxCustomOption | null]'
  }
]

  const exposedData = [
  {
    name: 'clear',
    description: 'Clears the current selection (sets the model to `null`). In\n`trigger="button"` mode the typed search query stays. In\n`trigger="input"` mode the input empties, because there it shows the\nselected option\'s label.',
    type: '() => void'
  },
  {
    name: 'focus',
    description: 'Moves focus to the input in `trigger="input"` mode, and to the button in\n`trigger="button"` mode. While the popover\'s search input is showing, it\nfocuses that instead.',
    type: '(options?: FocusOptions) => void'
  }
]
```
