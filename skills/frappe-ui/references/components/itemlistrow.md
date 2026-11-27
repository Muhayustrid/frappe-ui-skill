<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/ItemListRow/ItemListRow.md -->

# ItemListRow

A single row of a menu or list, with a prefix, a label and a suffix. It is the
row that `Dropdown`, `Select`, `Combobox` and `MultiSelect` render, so use it
to build a custom menu that matches them.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { ItemListRow } from 'frappe-ui'

const statuses = [
  { value: 'backlog', label: 'Backlog', dot: 'bg-surface-gray-5' },
  { value: 'todo', label: 'Todo', dot: 'bg-surface-blue-5' },
  { value: 'in-progress', label: 'In progress', dot: 'bg-surface-amber-5' },
  { value: 'done', label: 'Done', dot: 'bg-surface-green-5' },
  {
    value: 'archived',
    label: 'Archived',
    dot: 'bg-surface-gray-3',
    disabled: true,
  },
]

const selected = ref('todo')
const hovered = ref<string | null>(null)
</script>

<template>
  <div
    role="listbox"
    aria-label="Status"
    class="w-56 rounded-6 bg-surface-elevation-2 p-1.5 shadow-2xl"
    @mouseleave="hovered = null"
  >
    <ItemListRow
      v-for="status in statuses"
      :key="status.value"
      as="button"
      type="button"
      class="text-left"
      role="option"
      :aria-selected="selected === status.value"
      :disabled="status.disabled"
      :active="hovered === status.value"
      :selected="selected === status.value"
      :aria-disabled="status.disabled || undefined"
      @mouseenter="hovered = status.disabled ? null : status.value"
      @click="!status.disabled && (selected = status.value)"
    >
      <template #prefix>
        <span class="size-2 rounded-full" :class="status.dot" />
      </template>
      {{ status.label }}
      <template #suffix>
        <span
          v-if="selected === status.value"
          class="lucide-check size-4 text-ink-gray-6"
          aria-hidden="true"
        />
        <span v-else-if="status.disabled" class="text-xs text-ink-gray-4">
          Admins only
        </span>
      </template>
    </ItemListRow>
  </div>
</template>
```

## Behavior

### Slots

The default slot holds the label. `#label` replaces the default slot when you
pass both. `#prefix` and `#suffix` render only when they have content, so an
empty slot leaves no gap.

### Active and selected

`active` marks the row the pointer or keyboard is on. `selected` marks the
chosen value. Both give the row the same highlight. The row does not track
either state: set them from your own state, as the status menu does on
`mouseenter` and `click`.

### Disabled

`disabled` mutes the row and shows a not-allowed cursor. It does not stop
clicks or set the `disabled` attribute on the element, so skip the action in
your handler.

### Size

`size` is `xs`, `sm` (default), `md` or `lg`: rows at least 24, 28, 32 and 40px
tall. These match the input sizes, so a menu lines up with the trigger above
it.

### Element

The row renders a `div`. `as` sets another tag or a component, such as
`as="button"` or `as="a"`. Other attributes, such as `type`, `href` or
`role`, land on that element.

## Accessibility

The row sets no role. Add the roles your menu needs, such as `role="option"`
and `aria-selected` inside a `role="listbox"` container.

## API reference

```ts
const propsData = [
  {
    name: 'as',
    description: 'Element tag or component used for the row wrapper.',
    required: false,
    type: 'string | Component',
    default: '"div"'
  },
  {
    name: 'size',
    description: 'Shared row density preset.',
    required: false,
    type: 'InputSize',
    default: '"sm"'
  },
  {
    name: 'active',
    description: 'Highlights the row as the current active target.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'selected',
    description: 'Highlights the row as selected.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'disabled',
    description: 'Disables interaction and applies muted styling.',
    required: false,
    type: 'boolean',
    default: 'false'
  }
]

  const slotsData = [
  {
    name: 'default',
    description: '',
    type: 'any'
  },
  {
    name: 'prefix',
    description: '',
    type: 'any'
  },
  {
    name: 'label',
    description: '',
    type: 'any'
  },
  {
    name: 'suffix',
    description: '',
    type: 'any'
  }
]
```
