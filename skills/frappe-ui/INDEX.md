# What frappe-ui ships

Every public component, composable and helper in the library, grouped by the
entry point that exports it. (Exported TypeScript types are not listed.)
Use this to pick a component; then read its entry in [CORE.md](CORE.md) before
writing props. For the bundled upstream usage and API page, see [CATALOG.md](CATALOG.md).

**Path rule.** A name belongs to exactly one path: the section it appears under
is its import path. `frappe-ui` (the root) is the default. The subpath families
are separate entry points — a name from `frappe-ui/list` does not resolve from
the root, and a root name does not resolve from `frappe-ui/list`.

## Root: `frappe-ui`

```js
import { Button, Dialog, FormControl, useCall } from 'frappe-ui'
```

**Display.** `Alert` inline notice · `Avatar` image or initials · `Badge` status
pill · `Divider` rule · `Duration` duration field · `ErrorMessage` inline error ·
`Icon` one lucide/emoji/component glyph · `ItemListRow` listbox row primitive ·
`LoadingText` spinner + label · `Progress` linear bar · `Rating` stars ·
`Skeleton` load placeholder · `Spinner` busy indicator (`LoadingIndicator` is
its older alias) · `Toast` (via `toast.*`, not a component) · `Tooltip` hover
hint.

**Actions.** `Button` every trigger · `Dropdown` action menu · `ContextMenu`
right-click menu · `TabButtons` segmented control · `Tabs` content tabs ·
`KeyboardShortcut` kbd combo chip · `KeyboardShortcutsDialog` shortcut list.

**Overlays.** `Dialog` modal (`v-model:open`) · `BottomSheet` mobile sheet ·
`Popover` anchored panel · `HoverCard` hover preview · `SettingsDialog` settings
modal with its own nav. Imperative: `dialog.confirm`, `dialog.prompt`,
`dialog.danger` — there is no `dialog.alert`. Mount `FrappeUIProvider` once at
the root for both `dialog.*` and `toast.*`.

**Form controls.** `FormControl` labeled field, dispatches on `type` ·
`FormLabel` label markup for custom controls · `TextInput` · `Textarea` ·
`Password` · `Select` · `MultiSelect` · `Combobox` · `Checkbox` · `Radio` +
`RadioGroup` · `Switch` · `Slider` · `DatePicker` / `DateRangePicker` /
`DateTimePicker` / `TimePicker` · `FileUploader` (Frappe upload, no label).

**Providers and app-wide state.** `FrappeUIProvider` (mount once; renders the dialog and toast portals) · `ToastProvider` (rendered by it) · `Dialogs` (the imperatively opened dialog host) · `TooltipProvider` (groups tooltips so the skip-delay is shared) · `FrappeUI` app plugin · `toast`, `dialog`, `setConfig` / `getConfig`.

**Navigation and layout.** `DesktopShell` (slots `#rail` `#sidebar`) ·
`MobileShell` (slots default `#nav`) · `MobileNav` / `MobileNavItem` ·
`PageHeader` + `PageHeaderBase` / `PageHeaderTitle` / `PageHeaderMobile` /
`PageHeaderMobileTitle` / `PageHeaderBackButton` (headers teleport into the
shell's `PageHeaderTarget`) · `Sidebar` family (`SidebarHeader`,
`SidebarSection`, `SidebarLabel`, `SidebarItem`, `SidebarCollapseToggle`,
`SidebarCard`) · `SidebarRail` / `SidebarRailItem` · `ScrollArea` ·
`Breadcrumbs` · `Tree` hierarchical list · `SettingsDialog` family
(`SettingsSidebar`, `SettingsNavGroup`, `SettingsNavItem`, `SettingsContent`,
`SettingsPanel`, `SettingsHeader`, `SettingsBody`, `SettingsRow`).

**Data and state.** `useCall`, `useDoc`, `useList`, `useDoctype`, `useNewDoc`
([DATA.md](DATA.md)) · legacy `createResource` / `createListResource` /
`createDocumentResource` (supported through 1.x) · `useColorScheme` /
`useResolvedColorScheme` · `useKeyboardShortcut` · `usePageMeta` ·
`useFileUpload` / `FileUploadHandler` / `upload` · `useShellScrolled` /
`shellScrollContainer` · portal helpers (`providePortalTarget`,
`portalTargetKey`, `usePortalTarget`) · directives `vFocus` and
`vOnOutsideClick` · `frappeRequest` and `call` (HTTP transport).

## `frappe-ui/list`

```js
import { List, ListHeader, ListHeaderCell, ListRows, ListRow, ListCell } from 'frappe-ui/list'
```

`List` (feed by default, table with `:columns`) · `ListHeader` ·
`ListHeaderCell` · `ListHeaderCellSort` · `ListRows` · `ListRow` · `ListCell` ·
`ListGroup` · `ListRowBase`. Details in [CORE.md](CORE.md#list-family-frappe-uilist).

## `frappe-ui/charts`

```js
import { BarChart, NumberCard } from 'frappe-ui/charts'
```

`BarChart`, `LineChart`, `AreaChart`, `DonutChart`, `PercentageBarChart`,
`FunnelChart`, `HeatmapChart`, `CalendarHeatmapChart`, `ScatterChart`,
`SankeyChart`, `NumberCard` (KPI tile) + chrome `ChartCard`, `ChartContainer`,
`ChartLegend`, `ChartTooltip`. A page of charts is the Dashboard archetype in
[CORE.md](CORE.md), not a component.

## `frappe-ui/editor`

```js
import { Editor, EditorContent, RichTextKit, Bold } from 'frappe-ui/editor'
```

`Editor` (renderless, TipTap) + `EditorContent`, `EditorFixedMenu`,
`EditorBubbleMenu`, `EditorFloatingMenu`, `EditorTableMenu`, `EditorDropZone`,
kits `RichTextKit` / `CommentKit` / `InlineKit`, menu items
(`Bold`, `Italic`, `HeadingGroup`, `InsertLink`, …) and presets (`minimalToolbar`,
`commentToolbar`, `articleToolbar`, `tableToolbar`).

## `frappe-ui/code-editor`

```js
import { CodeEditor, CodeEditorContent, CodeKit, loadLanguage } from 'frappe-ui/code-editor'
```

`CodeEditor` (renderless, CodeMirror 6) + `CodeEditorContent`, engine
`useCodeEditor`, extension bundle `CodeKit`, single extensions `codeChrome` /
`codeHighlight` / `codeKeymap`, and `loadLanguage(key)` for the ten optional
`@codemirror/lang-*` peers. There is no labeled code field — build one.

## `frappe-ui/experimental` — unstable, no deprecation promise

```js
import { CommandPalette, CommandPaletteItem, Stepper } from 'frappe-ui/experimental'
```

`Accordion` collapsible sections · `Stepper` ordered progress (with sub-steps) ·
`ThemeSwitcher` light/dark/system preview cards (deprecated: `Select` +
`useColorScheme`) · `Calendar` day/week/month/agenda · `CommandPalette` +
`CommandPaletteInput` / `List` / `Group` / `Item` / `Empty` / `Footer` ·
`ListView` parked config-driven v0 list · v0 charts (`AxisChart`,
`NumberChart`, …) · `DateCalendar` / `DateRangeCalendar` · `PickerShell` ·
`FloatingWindow` · `MultiEmailInput` · `SpriteIcons` · `InputLabel` /
`InputDescription` / `InputError` / `useInputLabeling`.

Parked v0 names that do **not** resolve from the root: `Autocomplete`,
`ListView`, `TextEditor`, `CommandPalette`, `Calendar`, `ThemeSwitcher`, the v0
charts. Replacements are listed under "Names that moved" in [CORE.md](CORE.md).

## Exports that are not components

`frappe-ui/tailwind` (the preset), `frappe-ui/vite` (build plugin),
`frappe-ui/vitepress` (docs theme), `frappe-ui/style.css` (the one CSS import),
`frappe-ui/icons` (sprite trio, experimental), `frappe-ui/tsconfig.base.json`.
