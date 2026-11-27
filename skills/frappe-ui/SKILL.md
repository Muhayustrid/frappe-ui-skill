---
name: frappe-ui
description: Build consistent Frappe-style user interfaces using the frappe-ui Vue 3 component library and its design tokens. Use when scaffolding pages, forms, dialogs, lists, or any UI inside a Frappe-based app, when the user mentions frappe-ui, Frappe Cloud / Gameplan / Desk / Drive / Insights styling, or asks to "use frappe-ui components".
---

# frappe-ui

Compose frappe-ui's Vue 3 components and style them with its semantic Tailwind
tokens. This is the entry point: it pins the version, routes each job to the
reference that answers it, and lists the rules that hold everywhere. Props,
slots, events and token names are deliberately not repeated here — read the
matching entry first, then write markup.

## Shape of a page

```vue
<script setup>
import { Button, Dialog, FormControl } from 'frappe-ui'
import { ref } from 'vue'

const open = ref(false)
const name = ref('')
</script>

<template>
  <div class="p-4 bg-surface-base text-ink-gray-8">
    <Button variant="solid" theme="gray" @click="open = true">New Task</Button>
    <Dialog v-model:open="open" title="Create Task">
      <FormControl v-model="name" label="Title" required />
    </Dialog>
  </div>
</template>
```

Read the rest of `Button`, `Dialog` and `FormControl` in CORE.md before adding
props; the example shows the shape, not their full API.

## Step 1 — Pin the version

Run this in the folder whose `package.json` declares `frappe-ui`. In a Frappe
app that is often `frontend/`, `desk/`, `dashboard/` or the app root.

```bash
node -p "require('./node_modules/frappe-ui/package.json').version"
```

`require('frappe-ui/package.json')` does **not** work: the package's `exports`
map does not publish `package.json`. If the direct path is absent (a hoisted
workspace install), read the declaration and lockfile instead:

```bash
grep -n '"frappe-ui"' package.json package-lock.json yarn.lock pnpm-lock.yaml
npm ls frappe-ui 2>/dev/null || yarn why frappe-ui
```

| Installed | Action |
|---|---|
| `>= 1.0.0` | This skill applies. Go to step 2. |
| `0.1.x` | The component references here do **not** apply. See below. |
| not installed | Pin it per [SETUP.md](SETUP.md), then re-run this step. |

On `0.1.x` the components are the pre-1.0 set, and the installed package is the
only authority for what exists. `PageHeader`, `DesktopShell` and the
`frappe-ui/list` family are 1.0 additions; `Editor` (TipTap), `CodeEditor` and
the seven-part `CommandPalette` are 1.x rebuilds of old `0.1.x` components.
Other names exist in `0.1.x` with older APIs — `Sidebar`, `Calendar`, the
two-part `CommandPalette`, the old charts (`AxisChart`, `NumberChart`,
`ECharts`) and, in late releases, the `use*` composables. Never assume any 1.x
prop.

```bash
sed -n '1,200p' node_modules/frappe-ui/src/index.ts   # the export barrel
cat node_modules/frappe-ui/readme.md
```

Then read `src/components/<Name>/types.ts` for the props and slots of each
component you use. Never port a 1.x prop into a 0.1.x app: an undeclared prop
is silently forwarded to the root element or dropped, and a slot that does not
exist renders nothing.

Some late `0.1.x` releases already export the `use*` data composables. Check
before assuming [DATA.md](DATA.md) does not apply:

```bash
grep -n "data-fetching" node_modules/frappe-ui/src/index.ts
```

If it matches, compare the installed `node_modules/frappe-ui/src/data-fetching/useCall/types.ts`
with [DATA.md](DATA.md) before relying on an option: the composables shipped
before 1.0 in a slightly different shape.

## Step 2 — Route the job to one reference

Read the named section before the first line of markup for that job. **On
`0.1.x`, skip steps 2–4** — the installed package's readme, export barrel and
`types.ts` files from step 1 are your reference instead.

| You are writing | Read |
|---|---|
| "does the library have…?", or choosing a component | [INDEX.md](INDEX.md) |
| a component’s props, slots, events | [CORE.md](CORE.md) → its entry; [CATALOG.md](CATALOG.md) → local documentation and API |
| a page shell, sidebar, header, mobile layout, empty state, page archetypes | CORE.md → Page anatomy |
| colour, type, radius, shadow, dark mode | CORE.md → Tokens |
| a list, table or feed | CORE.md → `List` family |
| charts and KPI tiles | CORE.md → Charts and Calendar |
| rich text or a code field | CORE.md → Editor / CodeEditor |
| a server read or write | [DATA.md](DATA.md) → the matching composable or resource factory |
| directives, utilities, VitePress, or experimental APIs | [CATALOG.md](CATALOG.md) → Other or Experimental |
| a new project or build config | [SETUP.md](SETUP.md) |

Read the whole entry, not just its summary line: the accepted prop values, slot
names and model shapes are stated per component and deliberately differ from
one component to the next. When an entry does not answer a question, read the
installed source — `node_modules/frappe-ui/src/components/<Name>/types.ts` and
the generated `<Name>.api.md`. For upstream examples and API details, open only the matching local page via
[CATALOG.md](CATALOG.md). No web fetch is needed. When they differ, use the
installed package’s types and exports rather than the bundled snapshot.

## Step 3 — Follow the rules that hold everywhere

1. **Compose the library; do not imitate it.** Every control, overlay, list and
   menu comes from frappe-ui. Raw HTML is for layout only: the divs and grids
   that arrange components. If INDEX.md lists a component for the job, use it.

2. **Import from the path that owns the name.** The root is not a superset:

   | Name | Import from |
   |---|---|
   | `List`, `ListHeader`, `ListRows`, `ListRow`, `ListCell`, `ListGroup` | `frappe-ui/list` |
   | `Editor`, `EditorContent`, `EditorFixedMenu`, `RichTextKit`, `Bold`, … | `frappe-ui/editor` |
   | `CodeEditor`, `CodeEditorContent`, `CodeKit`, `loadLanguage` | `frappe-ui/code-editor` |
   | `BarChart` … `NumberCard`, `ChartCard`, `ChartLegend` | `frappe-ui/charts` |
   | `CommandPalette*`, `Calendar`, `Accordion`, `Stepper`, `ThemeSwitcher` | `frappe-ui/experimental` |
   | every other UI name | `frappe-ui` |

   Config and CSS entry points are not app imports: `frappe-ui/vite`,
   `frappe-ui/tailwind` and `frappe-ui/style.css` belong in config files. A
   name exported from one path is not re-exported from another; the wrong path
   is a build error, not a fallback.

3. **Read the entry before you use a prop or slot.** A prop the component does
   not declare is forwarded to the root element or dropped, and a slot it does
   not declare renders nothing. Both fail silently.

4. **The model is per component.** Do not generalise from one family to
   another:

   | Model | Components |
   |---|---|
   | `v-model` | text/textarea/password inputs, `Select`, `Combobox`, `MultiSelect`, `Checkbox`, `Switch`, `RadioGroup`, `Slider`, `Rating`, `Duration`, every date/time picker, `Tabs`, `TabButtons`, `Editor`, `CodeEditor`, `Accordion`, `Stepper` |
   | `v-model:open` | `Dialog`, `Popover`, `HoverCard`, `Dropdown`, `ContextMenu`, `BottomSheet`, `CommandPalette` (`Tooltip` has no open state) |
   | `v-model:selection` + `v-model:active` | `List` (`string[]` + `string`) |
   | `v-model:query` | `Combobox`, `MultiSelect`, `CommandPalette` |
   | `v-model:expanded` | `Tree` (array of keys) |
   | `v-model:tab` | `SettingsDialog` (`Tabs` takes plain `v-model`) |

   The value type is per component too: `Slider` is `number[]`, `Rating` a
   number, `DateRangePicker` `[from, to] | []`.

5. **Colour has two axes when the component has both.** `variant` is visual
   weight, `theme` is hue, and the accepted values differ per component —
   CORE.md states each. In your own markup, colour comes from the semantic
   scales only: `bg-surface-*`, `text-ink-*`, `border-outline-*`.

6. **Icons are class strings.** `<span class="lucide-edit size-4"
   aria-hidden="true" />` in markup; a prop named `icon` / `iconLeft` /
   `iconRight` takes the string `"lucide-edit"`. Never import per-icon
   components.

7. **Slots are per component, never universal.** `PageHeader` has a default
   slot only; `PageHeaderMobile` has `#prefix` and `#suffix`; `Tooltip` puts
   its trigger in the default slot and its content in `#content`. Check the
   component's entry or the overlay slot table in CORE.md before writing a
   `<template #…>`.

8. **Prefer composables for new Frappe data fetching.** `useCall`, `useList`, `useDoc`,
   `useDoctype` and `useNewDoc` are siblings with separate option lists, not
   wrappers around one another. Writes use `immediate: false` and
   `await submit(params)`; reads return a `reactive` object whose members you
   read directly (no `.value`), and `useDoc` exposes `doc`, not `data`.
   [DATA.md](DATA.md) documents both layers. Keep an existing `create*Resource`
   integration unless migration is part of the task; its API is different.

## Step 4 — Verify before calling it done

- **Imports resolve.** Run the build / type-check; no
  `Package subpath '…' is not defined by "exports"`.
- **Colour is semantic.** No raw palette classes (`bg-gray-*`, `text-gray-*`,
  `bg-slate-*`, `bg-white`); `text-white` is valid only on a solid
  `bg-surface-<color>-7` block.
- **Icons render.** No `~icons/` import, no `lucide-vue-next`, no `<LucideX>`
  tag; an icon-only button has an `aria-label`.
- **Models match step 3.** Every overlay has its declared open model, and no
  overlay is a hand-built `fixed inset-0` div.
- **Data states exist.** The page renders loading, error and empty, and the
  write path awaits its `submit`.
- **Fresh project:** run the checks in [SETUP.md](SETUP.md) → Sanity check.

## Files

| File | Contents |
|---|---|
| [INDEX.md](INDEX.md) | public names grouped by import path |
| [CATALOG.md](CATALOG.md) | local documentation and API pages, grouped by topic |
| [CORE.md](CORE.md) | tokens, page anatomy, component props/slots, list, editor, charts |
| [DATA.md](DATA.md) | composables and supported `create*Resource` factories |
| [SETUP.md](SETUP.md) | Tailwind v3 preset, subpaths, Vite plugin, CodeMirror peers |
| [evals/](evals/README.md) | regression harness: invocation + API correctness |

Packaging and install commands for another harness live in the skill
repository's `README.md`, one level above `skills/frappe-ui/`
(https://github.com/Muhayustrid/frappe-ui-skill), not in this folder.
