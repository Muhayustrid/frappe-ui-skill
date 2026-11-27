<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/MultiSelect/MultiSelect.md -->

# MultiSelect

A searchable list that picks several options, with Clear All and Select All
buttons in its footer. To pick one option, use [Combobox](combobox.md) when the
list needs search and [Select](select.md) when it does not.



## Examples

### Assign reviewers

`#prefix` shows a stack of up to three avatars and a "+N" count for the rest.
`#summary` lists the selected names.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { Avatar, MultiSelect } from 'frappe-ui'

type Member = {
  label: string
  value: string
  image: string
  role: string
}

const members: Member[] = [
  {
    label: 'Alex Rivera',
    value: 'alex@example.com',
    image: 'https://i.pravatar.cc/80?u=alex@example.com',
    role: 'Engineering',
  },
  {
    label: 'Priya Shah',
    value: 'priya@example.com',
    image: 'https://i.pravatar.cc/80?u=priya@example.com',
    role: 'Design',
  },
  {
    label: 'Marcus Lee',
    value: 'marcus@example.com',
    image: 'https://i.pravatar.cc/80?u=marcus@example.com',
    role: 'Product',
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
]

const value = ref<string[]>(['alex@example.com', 'priya@example.com'])

const MAX_AVATARS = 3

const visibleSelected = computed(() =>
  (
    value.value
      .map((v) => members.find((m) => m.value === v))
      .filter(Boolean) as Member[]
  ).slice(0, MAX_AVATARS),
)

const overflowCount = computed(() =>
  Math.max(0, value.value.length - MAX_AVATARS),
)
</script>

<template>
  <MultiSelect
    v-model="value"
    :options="members"
    placeholder="Assign reviewers…"
    class="w-80"
  >
    <template #prefix>
      <div v-if="visibleSelected.length" class="flex -space-x-1.5">
        <Avatar
          v-for="m in visibleSelected"
          :key="m.value"
          :image="m.image"
          :label="m.label"
          size="sm"
        />
        <span
          v-if="overflowCount > 0"
          class="z-10 grid size-5 place-items-center rounded-full bg-surface-gray-3 text-p-xs-medium text-ink-gray-7"
        >
          +{{ overflowCount }}
        </span>
      </div>
      <span v-else class="lucide-users size-4 text-ink-gray-5" />
    </template>

    <template #summary="{ selectedOptions, summary }">
      <template v-if="selectedOptions.length">
        {{ selectedOptions.map((o) => o.label).join(', ') }}
      </template>
      <template v-else>{{ summary }}</template>
    </template>

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
  </MultiSelect>
</template>
```

### Search people on the server

Bind `v-model:query`, fetch as the query changes, and pass the results to
`options`. See [Server search](#server-search) for the four things to handle.

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import { useDebounceFn } from '@vueuse/core'
import { Avatar, MultiSelect } from 'frappe-ui'

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
    label: 'Alexei Volkov',
    value: 'alexei@example.com',
    image: 'https://i.pravatar.cc/80?u=alexei@example.com',
    role: 'Engineering',
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
    label: 'Marco Silva',
    value: 'marco@example.com',
    image: 'https://i.pravatar.cc/80?u=marco@example.com',
    role: 'Engineering',
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
    label: 'Sophie Laurent',
    value: 'sophie@example.com',
    image: 'https://i.pravatar.cc/80?u=sophie@example.com',
    role: 'Sales',
  },
  {
    label: 'Kenji Tanaka',
    value: 'kenji@example.com',
    image: 'https://i.pravatar.cc/80?u=kenji@example.com',
    role: 'Design',
  },
  {
    label: 'Kenta Mori',
    value: 'kenta@example.com',
    image: 'https://i.pravatar.cc/80?u=kenta@example.com',
    role: 'Engineering',
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
  {
    label: 'Lina Petrova',
    value: 'lina@example.com',
    image: 'https://i.pravatar.cc/80?u=lina@example.com',
    role: 'Marketing',
  },
  {
    label: 'Liam Connor',
    value: 'liam@example.com',
    image: 'https://i.pravatar.cc/80?u=liam@example.com',
    role: 'Product',
  },
  {
    label: 'Hassan Iqbal',
    value: 'hassan@example.com',
    image: 'https://i.pravatar.cc/80?u=hassan@example.com',
    role: 'Sales',
  },
  {
    label: 'Ava Nguyen',
    value: 'ava@example.com',
    image: 'https://i.pravatar.cc/80?u=ava@example.com',
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

const value = ref<string[]>([])
const query = ref('')
const results = ref<Member[]>([])
const loading = ref(false)
const knownById = ref(new Map<string, Member>())

let requestId = 0
async function fetchMembers(query: string) {
  const id = ++requestId
  loading.value = true
  const members = await searchMembersApi(query)
  // Drop stale responses so an earlier-but-slower request can't overwrite
  // the latest results.
  if (id !== requestId) return
  results.value = members
  for (const m of members) knownById.value.set(m.value, m)
  loading.value = false
}

const onQueryChange = useDebounceFn(fetchMembers, 250)

// Merge currently-selected members into the options so chips stay
// resolvable after the query narrows the result set.
const options = computed<Member[]>(() => {
  const byId = new Map<string, Member>()
  for (const m of results.value) byId.set(m.value, m)
  for (const id of value.value) {
    if (!byId.has(id)) {
      const existing = knownById.value.get(id)
      if (existing) byId.set(id, existing)
    }
  }
  return Array.from(byId.values())
})

function onOpen(isOpen: boolean) {
  if (!isOpen) return
  // Listening for `@update:query` hands ownership of the query over, so the
  // open-time reset MultiSelect does for an unbound query is ours to do.
  // Without it the last search text stays in the box.
  query.value = ''
  if (results.value.length === 0) fetchMembers('')
}
</script>

<template>
  <!-- The `as` casts on the two handlers below exist because these model
       emits are declared twice; drop them when #1096 removes the duplicates. -->
  <MultiSelect
    v-model="value"
    v-model:query="query"
    :options="options"
    :loading="loading"
    :filterable="false"
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
  </MultiSelect>
</template>
```

### Permissions

With a short, fixed list, `#summary` can list every selected label in place of
the default "2 selected".

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { MultiSelect } from 'frappe-ui'

const value = ref<string[]>(['read', 'write'])

const options = [
  { label: 'Read', value: 'read' },
  { label: 'Write', value: 'write' },
  { label: 'Delete', value: 'delete' },
  { label: 'Share', value: 'share' },
]

// The default summary reads "2 selected" past one selection. Joining the
// labels keeps a short, fixed list readable at a glance.
function labelsFor(values: string[]) {
  return options
    .filter((o) => values.includes(o.value))
    .map((o) => o.label)
    .join(', ')
}
</script>

<template>
  <MultiSelect
    v-model="value"
    :options="options"
    placeholder="Select permissions"
    class="w-72"
  >
    <template #summary="{ summary }">
      {{ value.length ? labelsFor(value) : summary }}
    </template>
  </MultiSelect>
</template>
```

### Issue labels

`#trigger` shows each selected option as a `Badge` with a remove button.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Badge, MultiSelect, type BadgeProps } from 'frappe-ui'

type Tag = {
  label: string
  value: string
  // Derived from Badge, never hand-written. A copied union is how the
  // deprecated `orange` theme outlived the ADR-0008 sweep: nothing failed when
  // the real union changed. Note `tsconfig.app.json` excludes `stories/**`, so
  // this does not fail CI today — it fails in the editor, and it fails the
  // moment stories join the type-check program.
  theme: BadgeProps['theme']
}

const tags = ref<string[]>(['bug', 'p0'])

const tagOptions: Tag[] = [
  { label: 'Bug', value: 'bug', theme: 'red' },
  { label: 'Feature', value: 'feature', theme: 'blue' },
  { label: 'Enhancement', value: 'enhancement', theme: 'green' },
  { label: 'P0', value: 'p0', theme: 'red' },
  { label: 'P1', value: 'p1', theme: 'amber' },
  { label: 'P2', value: 'p2', theme: 'gray' },
  { label: 'Frontend', value: 'frontend', theme: 'blue' },
  { label: 'Backend', value: 'backend', theme: 'gray' },
  { label: 'Docs', value: 'docs', theme: 'green' },
]

function removeTag(value: string | number) {
  tags.value = tags.value.filter((v) => v !== value)
}
</script>

<template>
  <MultiSelect v-model="tags" :options="tagOptions">
    <template #trigger="{ open, selectedOptions, setOpen }">
      <button
        type="button"
        :data-state="open ? 'open' : 'closed'"
        class="flex w-96 min-h-8 cursor-pointer items-center gap-1.5 rounded-4 border border-[--surface-gray-2] px-1.5 py-1 text-left transition-colors hover:border-outline-elevation-2 data-[state=open]:focus-ring"
        @click="setOpen(!open)"
      >
        <div class="flex min-w-0 flex-1 flex-wrap items-center gap-1">
          <Badge
            v-for="option in selectedOptions"
            :key="option.value"
            :theme="(option as Tag).theme"
            size="md"
          >
            {{ option.label }}
            <template #suffix>
              <span
                role="button"
                tabindex="-1"
                class="-mr-0.5 inline-flex cursor-pointer items-center justify-center rounded-1 p-0.5 opacity-70 hover:opacity-100"
                @click.stop="removeTag(option.value)"
                @pointerdown.stop
              >
                <span class="lucide-x size-3" />
              </span>
            </template>
          </Badge>

          <span
            v-if="!selectedOptions.length"
            class="px-1 text-base text-ink-gray-4"
          >
            Add tags…
          </span>
        </div>

        <span
          :class="[
            'lucide-chevron-down size-4 shrink-0 text-ink-gray-4 transition-transform',
            open && 'rotate-180',
          ]"
        />
      </button>
    </template>
  </MultiSelect>
</template>
```

### Assign button in a toolbar

`#trigger` replaces the default trigger with a `Button` that shows a count.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button, MultiSelect } from 'frappe-ui'

const state = ref<string[]>(['alice'])

const options = [
  { label: 'Alice Rivera', value: 'alice' },
  { label: 'Bao Nguyen', value: 'bao' },
  { label: 'Chen Wei', value: 'chen' },
  { label: 'Diego Ruiz', value: 'diego' },
  { label: 'Elena Park', value: 'elena' },
]
</script>

<template>
  <MultiSelect v-model="state" :options="options" placeholder="Assign to">
    <template #trigger="{ selectedOptions, open }">
      <Button icon-left="lucide-users">
        {{
          selectedOptions.length
            ? `${selectedOptions.length} assigned`
            : 'Assign to'
        }}
        <template #suffix>
          <span
            :class="[
              'lucide-chevron-down size-4 transition-transform',
              open && 'rotate-180',
            ]"
          />
        </template>
      </Button>
    </template>
  </MultiSelect>
</template>
```

### Bulk actions footer

`#footer` replaces the default Clear All and Select All footer. It receives
`selectAll` along with the other control props.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button, MultiSelect } from 'frappe-ui'

const state = ref<string[]>([])

const options = [
  { value: 'red-apple', label: 'Red Apple' },
  { value: 'blueberry-burst', label: 'Blueberry Burst' },
  { value: 'orange-grove', label: 'Orange Grove' },
  { value: 'banana-split', label: 'Banana Split' },
  { value: 'grapes-cluster', label: 'Grapes Cluster' },
  { value: 'kiwi-slice', label: 'Kiwi Slice' },
  { value: 'mango-fusion', label: 'Mango Fusion' },
]
</script>

<template>
  <MultiSelect v-model="state" :options="options" class="w-64">
    <template #footer="{ clear, selectAll, selectedOptions }">
      <div
        class="flex items-center justify-between gap-2 border-t border-outline-gray-1 px-2 py-1.5"
      >
        <Button theme="red" variant="ghost" @click="clear">
          <template #prefix>
            <span class="lucide-trash-2 size-4" />
          </template>
          Clear ({{ selectedOptions.length }})
        </Button>

        <Button variant="ghost" @click="selectAll">
          <template #prefix>
            <span class="lucide-check-check size-4" />
          </template>
          Select All
        </Button>
      </div>
    </template>
  </MultiSelect>
</template>
```

## Behavior

### Options

`options` takes `{ label, value }` objects or groups of `{ group, options }`.
A group label shows above its options. An option is selected when its `value`
is in the model.

### Selected options

`@update:modelValue` gives the selected values. `@update:selectedOptions`
fires with it and gives the matching option objects from `options`, custom
fields included. Use it instead of looking the objects up yourself.

```vue
<MultiSelect
  v-model="value"
  :options="members"
  @update:selectedOptions="(options) => (emails = options.map((o) => o.email))"
/>
```

When nothing is selected, the value is `[]`.

### Trigger summary

With one option selected, the trigger shows that option's label. With two or
more, it reads "N selected". `#summary` replaces that text. It receives the
default text as `summary`, so you can fall back to it, for example when
nothing is selected.

### Prefix

When you pass `#prefix`, it fills the whole prefix area for any number of
selected options, including none.

### Server search

To load options from a server:

1. Pass `:filterable="false"`, so the list does not filter again what the
   server already matched.
2. Drop old responses, for example with a request id, so a slow earlier
   request cannot overwrite newer results.
3. Keep the selected options in `options`, so they can still be shown after
   the results change.
4. Clear the query yourself when the popover opens. See
   [Search query](#search-query).

While `loading` is set, a spinner shows in the search row. The options you
passed stay visible and can still be picked, so people can keep choosing from
the last results. With `hide-search` there is no search row, so a loading row
replaces the results. In both cases the "No results" text is hidden while
loading.

### Search query

`v-model:query` is optional. Listening for `@update:query` counts as binding
it: there is no way to only watch the query. Once you bind it, the component
stops clearing the search box, so the last text stays in it. If you listen,
also bind `v-model:query` and clear it on `@update:open`.

To read the query without taking control of it, use `#search-prefix`,
`#search-suffix` or `#footer`. Each receives the query.

### Search row

`#search-prefix` and `#search-suffix` add content around the search input.
Both receive `{ query, setQuery, disabled, focus }`. `setQuery('')` clears the
query, and `focus()` moves focus back to the search input. The slots are
inside the search row, so `hide-search` removes them too.

### Item slots

`#item-prefix`, `#item-label` and `#item-suffix` change parts of the standard
row. `#item` replaces the whole row, including its outer element.

### Trigger slots

`#trigger` replaces the default button. It receives `open`, `disabled`,
`query`, `selectedOptions`, `clear`, `setOpen` and `close`. `#footer` receives
the same props and `selectAll`. `close()` is the same as `setOpen(false)`.

### Label, description and error

`label` renders above the trigger and `description` below it. `error` renders
below the trigger and hides `description`. It takes a string, an array of
strings (one line each), or an `Error`, the same values as
[ErrorMessage](errormessage.md). An empty string or an empty array means no
error. `required` adds a red asterisk to the label.

The `#label` slot replaces the label text and the required marker, and receives
`{ required }`. A `#description` slot is not hidden by `error`. It renders
above the error.

## Accessibility

While `error` is set, the trigger gets `aria-invalid` and an
`aria-errormessage` that points to the error text.

## API reference

```ts
const propsData = [
  {
    name: 'modelValue',
    description: 'Array of selected option values.',
    required: false,
    type: '(string | number)[]',
    default: '[]'
  },
  {
    name: 'options',
    description: 'Options rendered in the popover.',
    required: false,
    type: 'MultiSelectOptions',
    default: '[]'
  },
  {
    name: 'variant',
    description: 'Visual style of the trigger.',
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
    description: 'Disables the multi-select.',
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
    description: 'Controls the in-popover search query. Optional — the component owns the\nquery when this is not bound, so `v-model:query` is never required.\n\nWhen it is bound the consumer owns the query: the component never resets\nit on its own — not on open, not on close, not on mount, not on `clear()`.\nOnly typing (or the `setQuery` slot prop) changes it, and a seeded query\nfilters the list immediately. Unbound, the query still clears every time\nthe popover opens.',
    required: false,
    type: 'string',
    default: '""'
  },
  {
    name: 'hideSearch',
    description: 'Hides the in-popover search input.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'loading',
    description: 'Marks a fetch as in flight. With the search row showing, a spinner appears\nin it and the options you passed stay selectable; under `hide-search` a\nloading row replaces the results instead.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'filterable',
    description: 'Client-side query filtering. Defaults to `true`. Set to `false` for\npickers whose options come from a server search — the backend already\ndecided what matches, and a second literal substring pass on the client\nsilently drops fuzzy, ranked, or id-based results. This turns off query\nfiltering only; nothing else about the component changes.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'emptyText',
    description: 'Fallback empty-state copy.',
    required: false,
    type: 'string',
    default: '"No results"'
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
    type: 'MultiSelectSlotProps'
  },
  {
    name: 'prefix',
    description: 'Content rendered before the trigger label. When provided, this slot\nowns the entire prefix area regardless of selection count — useful\nfor aggregate visuals like stacked avatars. If omitted, the trigger\nauto-renders the selected option\'s `#item-prefix` / `icon` when\nexactly one is selected, and nothing otherwise.',
    type: 'MultiSelectSlotProps'
  },
  {
    name: 'summary',
    description: 'Overrides the trigger label region. Receives the default summary\ntext as `summary` — use it as a fallback. Useful when you want to\nshow comma-separated labels (or any other format) instead of the\ndefault `"N selected"` for multi-selection states.',
    type: 'MultiSelectSummarySlotProps'
  },
  {
    name: 'suffix',
    description: 'Content rendered after the trigger label. Providing this slot\n**replaces the default chevron** — render your own fallback when\nyour slot content is conditional. Use `@click.stop` and\n`@pointerdown.stop` so the press doesn\'t toggle the popover.',
    type: 'MultiSelectSlotProps'
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
    name: 'search-prefix',
    description: 'Content rendered before the in-popover search input. Renders inside the\nsearch row, so it is not rendered at all when `hide-search` is set.',
    type: 'MultiSelectSearchSlotProps'
  },
  {
    name: 'search-suffix',
    description: 'Content rendered after the in-popover search input and loading\nindicator. Renders inside the search row, so it is not rendered at all\nwhen `hide-search` is set.',
    type: 'MultiSelectSearchSlotProps'
  },
  {
    name: 'group-label',
    description: 'Custom renderer for group labels.',
    type: 'MultiSelectGroupLabelSlotProps'
  },
  {
    name: 'empty',
    description: 'Fallback content rendered when there are no results.',
    type: 'MultiSelectEmptySlotProps'
  },
  {
    name: 'footer',
    description: 'Replaces the default Clear All / Select All footer. Receives the shared\ncontrol slot props plus `selectAll`.',
    type: 'MultiSelectFooterSlotProps'
  },
  {
    name: 'item',
    description: 'Replaces the entire row.',
    type: 'MultiSelectItemSlotProps'
  },
  {
    name: 'item-prefix',
    description: 'Shared content rendered before the standard row label.',
    type: 'MultiSelectItemSlotProps'
  },
  {
    name: 'item-label',
    description: 'Shared content rendered for the standard row label area.',
    type: 'MultiSelectItemSlotProps'
  },
  {
    name: 'item-suffix',
    description: 'Shared content rendered after the standard row label area.',
    type: 'MultiSelectItemSlotProps'
  }
]

  const emitsData = [
  {
    name: 'update:open',
    description: 'Fired when the open state changes.',
    type: '[value: boolean]'
  },
  {
    name: 'update:modelValue',
    description: 'Fired when the model value changes.',
    type: '[value: (string | number)[]]'
  },
  {
    name: 'update:query',
    description: 'Fired when the query changes.',
    type: '[value: string]'
  },
  {
    name: 'update:selectedOptions',
    description: 'Fired alongside `update:modelValue` with the original option objects\nresolved out of `options`, so custom fields on an option survive.',
    type: '[value: MultiSelectOption[]]'
  },
  {
    name: 'highlight',
    description: 'Fired when the highlighted row changes, by arrow keys or the pointer, with\nthe original option object. `null` once the popover closes.',
    type: '[option: MultiSelectOption | null]'
  }
]

  const exposedData = [
  {
    name: 'clear',
    description: 'Clears the current selection. It leaves the search query alone.',
    type: '() => void'
  },
  {
    name: 'focus',
    description: 'Moves focus to the trigger.',
    type: '(options?: FocusOptions) => void'
  }
]
```
