<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/experimental/ThemeSwitcher/ThemeSwitcher.md -->

# ThemeSwitcher

A labeled control for choosing between **light**, **dark**, and **system**
appearance. Each option is a preview card depicting that mode, and selecting one
drives the global `<html data-theme>` through [`useColorScheme`](#usecolorscheme), so a bare
`<ThemeSwitcher />` switches the whole app with no wiring.

> **Parked** — `ThemeSwitcher` left the root export in `1.0.0` and now ships
> from [`frappe-ui/experimental`](../experimental.md), exempt from the
> deprecation policy. It stays deprecated: build the control from
> [`useColorScheme`](#usecolorscheme) plus `Select` or `Dropdown` in new code.

```ts
import { ThemeSwitcher } from 'frappe-ui/experimental'
import type { ThemeSwitcherProps } from 'frappe-ui/experimental'

// The composable behind it stays on the root export:
import { useColorScheme } from 'frappe-ui'
```

```vue
<script setup lang="ts">
import { ThemeSwitcher } from '..'
</script>

<template>
  <ThemeSwitcher />
</template>
```

## Branding

Pass a `name` and a `logo` (an image URL or a Component) to show your branding
inside the theme panel previews. The `label` and `description` props set the heading of the component.

```vue
<script setup lang="ts">
import { ThemeSwitcher } from '..'
</script>

<template>
  <ThemeSwitcher
    name="CRM"
    logo="https://raw.githubusercontent.com/frappe/crm/develop/.github/logo.svg"
    label="Appearance"
    description="Pick how your branding looks on different theme modes"
  />
</template>
```

## Toggle button

In a header or sidebar you rarely want the full card preview. You want a single
labelled button that flips the state. Build one from the same
[`useColorScheme`](#usecolorscheme) composable: because both controls share the one
`<html data-theme>`, they stay in sync, and the button drops straight into a
sidebar or menu.

```vue
<script setup lang="ts">
import { Button, useColorScheme } from 'frappe-ui'

// The full ThemeSwitcher is for settings panels; in a header, sidebar, or menu
// you usually want a single labelled button. Build it from the same
// `useColorScheme` composable so both controls drive the one
// `<html data-theme>`. Drops straight into a frappe-ui sidebar.
const { colorScheme, toggleColorScheme } = useColorScheme()
</script>

<template>
  <Button @click="toggleColorScheme">
    Toggle theme
    <template #prefix>
      <LucideSun
        v-if="colorScheme === 'dark'"
        class="size-4"
      />
      <LucideMoon
        v-else
        class="size-4"
      />
    </template>
  </Button>
</template>
```

## In a user menu

A user menu is the most common home for theme switching. Nest the three options
as a hover submenu inside a [`Dropdown`](../components/dropdown.md), drive them through the same
[`useColorScheme`](#usecolorscheme) singleton, and mark the active one with `selected` so the menu
always reflects the shared `<html data-theme>`.

```vue
<script setup lang="ts">
import { computed } from 'vue'
import {
  Avatar,
  Dropdown,
  useColorScheme,
  type DropdownOptions,
} from 'frappe-ui'

const { colorScheme, setColorScheme } = useColorScheme()

const themes = [
  { label: 'Light', icon: 'lucide-sun', value: 'light' },
  { label: 'Dark', icon: 'lucide-moon', value: 'dark' },
  { label: 'System', icon: 'lucide-monitor', value: 'system' },
] as const

const menuOptions = computed<DropdownOptions>(() => [
  { icon: 'lucide-user', label: 'My Profile', onClick: () => {} },
  {
    icon: 'lucide-sun-moon',
    label: 'Theme',
    submenu: themes.map((theme) => ({
      label: theme.label,
      icon: theme.icon,
      selected: colorScheme.value === theme.value,
      onClick: () => setColorScheme(theme.value),
    })),
  },
  { icon: 'lucide-log-out', label: 'Log out', onClick: () => {} },
])

const crmLogo = 'https://raw.githubusercontent.com/frappe/crm/develop/.github/logo.svg'
</script>

<template>
  <Dropdown :options="menuOptions">
    <template #default="{ open }">
      <button
        class="flex items-center gap-2 rounded-5 px-2 py-2"
        :class="open ? 'bg-surface-gray-3' : 'hover:bg-surface-gray-2'"
      >
        <Avatar shape='square' :image="crmLogo" label="CRM" />
        <div class="text-start">
          <div class="text-base font-medium leading-none text-ink-gray-9">
            CRM
          </div>
          <div class="mt-1 text-sm text-ink-gray-7">faris@example.com</div>
        </div>
        <LucideChevronDown class="ml-2 size-4 text-ink-gray-5" />
      </button>
    </template>

    <template #item-suffix="{ selected }">
      <LucideCheck v-if="selected" class="size-4 text-ink-gray-7" />
    </template>
  </Dropdown>
</template>
```

## useColorScheme

The component is backed by the `useColorScheme` composable, exported from the
library. Its state is a shared singleton, so every consumer (the switcher, a
sidebar toggle, a user-menu entry) stays in sync with the single
`<html data-theme>` source of truth.

There is nothing to set up. The first `useColorScheme()` call restores the saved
preference (falling back to `system`) and starts following the OS setting.
Calling it near your app root simply makes that happen as early as possible.

```ts
import { useColorScheme } from 'frappe-ui'

const { colorScheme, setColorScheme, toggleColorScheme } = useColorScheme()
```

The selection persists to `localStorage` under the `theme` key and is reapplied
on the next load.

| Member              | Type                            | Description                                                              |
| ------------------- | ------------------------------- | ------------------------------------------------------------------------ |
| `colorScheme`       | `Readonly<Ref<ColorScheme>>`    | The selection: `'light' \| 'dark' \| 'system'`. Read-only.               |
| `setColorScheme`    | `(scheme: ColorScheme) => void` | Selects a scheme, applies `data-theme`, and persists it.                 |
| `toggleColorScheme` | `() => void`                    | Flips between light and dark.                                            |

`colorScheme` is read-only because the ref is only half the state — the other
half is the `data-theme` attribute and the stored value. Assigning to it would
move the ref and leave the document and `localStorage` behind. Go through
`setColorScheme`.

> **`colorScheme`, not `theme`.** Everywhere else in the library `theme` means a
> color tone (`theme="blue"` on a Button). The `data-theme` attribute and the
> `theme` storage key keep the older name so app CSS and saved preferences
> keep working.

> **Avoid the flash.** The scheme is applied from JavaScript once the app loads,
> so a page that ships without a `data-theme` briefly shows the default before
> switching. Set an initial `data-theme` on your `<html>`, or inline a small
> script that reads `localStorage.theme`, to render the right one from the first
> paint.

## API reference

```ts
const propsData = [
  {
    name: 'modelValue',
    description: 'Selected theme. Falls back to the shared `useColorScheme` state when unbound.',
    required: false,
    type: 'ColorScheme'
  },
  {
    name: 'label',
    description: 'Heading rendered above the options.',
    required: false,
    type: 'string',
    default: '"Theme"'
  },
  {
    name: 'description',
    description: 'Helper text rendered below the heading.',
    required: false,
    type: 'string',
    default: '"Switch between light, dark, or system theme"'
  },
  {
    name: 'logo',
    description: 'Brand logo shown inside each preview. A string is treated as an image\nsource; a component value is rendered with `<component :is>`.',
    required: false,
    type: 'string | Component'
  },
  {
    name: 'name',
    description: 'Brand name shown inside each preview.',
    required: false,
    type: 'string',
    default: '""'
  },
  {
    name: 'themeLabels',
    description: 'Overrides the per-option labels. For richer per-item content, use the\n`#item-label` slot, which takes precedence over this prop.',
    required: false,
    type: 'Partial<Record<ColorScheme, string>>'
  }
]

  const slotsData = [
  {
    name: 'label',
    description: 'Overrides the heading content.',
    type: 'any'
  },
  {
    name: 'description',
    description: 'Overrides the helper-text content.',
    type: 'any'
  },
  {
    name: 'item-label',
    description: 'Overrides a single option\'s label. Receives the option\'s `value`.\nFalls back to the `themeLabels` prop, then the built-in label.',
    type: '{ value: ColorScheme; }'
  }
]

  const emitsData = [
  {
    name: 'update:modelValue',
    description: 'Fired when the selected theme changes.',
    type: '[theme: ColorScheme]'
  }
]
```
