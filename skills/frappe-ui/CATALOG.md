# Official documentation catalog

Open only the matching local page for usage and API details; no web request is
needed. Start with [INDEX.md](INDEX.md) for import paths. The installed package
takes precedence when it differs from the snapshot.

Snapshot: [frappe/frappe-ui at `b435c66b51c0`](https://github.com/frappe/frappe-ui/tree/b435c66b51c0b19425a2a049b43e3ef2e308a8ab), retrieved 2026-11-27.
[MIT license](references/LICENSE.md). Pages include upstream prose, inlined API
metadata where available, and matching repository examples where available.
Interactive previews are omitted.

## Getting started

Follow [SETUP.md](SETUP.md) for install and build configuration. Check migration and the changelog before adopting newer APIs.

- [Introduction](references/introduction.md): what frappe-ui is, motivation, products using it.
- [Installation](references/getting-started.md): the install every setup shares: Tailwind preset wiring, `FrappeUIProvider`, TypeScript config.
- [Frappe app](references/getting-started/frappe.md): the Vite plugin, dev proxy, CSRF, boot data, the `www` page and the production build.
- [Standalone app](references/getting-started/standalone.md): a plain Vite app.
- [Tailwind setup](references/getting-started/tailwind.md): wiring the preset into an app, what it replaces, the `content` export.
- [Migration from v0](references/migration.md): moving an app off the v0 API, including every removed component with its replacement and every component parked in `frappe-ui/experimental`.
- [Changelog](references/changelog.md): notable changes per release.

## Foundations

Use [CORE.md → Tokens](CORE.md#tokens) for semantic utilities. Chart colors are a separate system.

- [Colors](references/foundations/colors.md): the `bg-surface-*`, `text-ink-*` and `border-outline-*` tokens, and the fixed palette.
- [Typography](references/foundations/typography.md): the type scale.
- [Radius](references/foundations/radius.md): radius scale.
- [Shadows](references/foundations/shadows.md): shadow scale and the surfaces to pair them with.
- [Focus ring](references/foundations/focus-ring.md): the shared focus treatment.

## Components

Import UI components from `frappe-ui`. For the Toast page, import `toast`, not `Toast`. See [CORE.md](CORE.md) for local recipes.

- [Alert](references/components/alert.md): inline status row or banner.
- [Avatar](references/components/avatar.md): user/entity avatar with image or label-derived initials.
- [Badge](references/components/badge.md): status pill.
- [BottomSheet](references/components/bottomsheet.md): panel that slides up from the bottom edge.
- [Breadcrumbs](references/components/breadcrumbs.md): page-header trail.
- [Button](references/components/button.md): primary trigger element.
- [Checkbox](references/components/checkbox.md): boolean input.
- [Combobox](references/components/combobox.md): searchable single select; use a separate query model for remote search.
- [ContextMenu](references/components/contextmenu.md): right-click menu for surfaces, rows and canvas elements.
- [DatePicker](references/components/datepicker.md): date input.
- [DesktopShell](references/components/desktopshell.md): the desktop app frame.
- [Dialog](references/components/dialog.md): modal overlay.
- [Divider](references/components/divider.md): horizontal/vertical rule.
- [Dropdown](references/components/dropdown.md): action menu anchored to a trigger.
- [Duration](references/components/duration.md): text input for a length of time.
- [ErrorMessage](references/components/errormessage.md): inline error display.
- [FileUploader](references/components/fileuploader.md): Frappe-native file upload with progress.
- [FormControl](references/components/formcontrol.md): the default labeled-field wrapper and a thin dispatcher.
- [FormLabel](references/components/formlabel.md): label text plus the required indicator, for custom controls that need the same markup.
- [FrappeUIProvider](references/components/frappeuiprovider.md): mounts the imperative `dialog.*` and `toast.*` portals. Wrap the app root with it once.
- [HoverCard](references/components/hovercard.md): floating panel on pointer rest or focus, for profile and link previews. Sighted-only context.
- [Icon](references/components/icon.md): renders one icon from any accepted form (a `lucide-*` string, an emoji, or a component). Most components take an icon prop and render it through `Icon` internally.
- [ItemListRow](references/components/itemlistrow.md): single-row list item primitive.
- [KeyboardShortcut](references/components/keyboardshortcut.md): renders a combo such as `Mod+K` as styled keys.
- [KeyboardShortcutsDialog](references/components/keyboardshortcutsdialog.md): lists every shortcut registered with `useKeyboardShortcut`. Mount once, bind `v-model:open`.
- [LoadingText](references/components/loadingtext.md): Spinner with a label, for inline "Loading…" states.
- [MobileShell](references/components/mobileshell.md): the mobile app frame.
- [MultiSelect](references/components/multiselect.md): multi-value select.
- [PageHeader](references/components/pageheader.md): app-shell headers (`PageHeader`, `PageHeaderMobile`, `PageHeaderBase`). A page declares its header and it renders into the layout's `<PageHeaderTarget />`.
- [Password](references/components/password.md): masked input with show/hide.
- [Popover](references/components/popover.md): anchored floating panel.
- [Progress](references/components/progress.md): linear progress bar.
- [Radio](references/components/radio.md): single choice from a set.
- [Rating](references/components/rating.md): star-rating input.
- [ScrollArea](references/components/scrollarea.md): styled cross-browser scroll container with overlay scrollbars. A general primitive, not app-shell specific.
- [Select](references/components/select.md): fixed-options single-value select.
- [SettingsDialog](references/components/settingsdialog.md): settings modal with a sidebar of grouped tabs.
- [Sidebar](references/components/sidebar.md): app-shell sidebar primitive.
- [SidebarRail](references/components/sidebarrail.md): the narrow icon column of an app shell.
- [Skeleton](references/components/skeleton.md): placeholder block shown in place of content while data loads.
- [Slider](references/components/slider.md): numeric range input.
- [Spinner](references/components/spinner.md): indeterminate loading state.
- [Switch](references/components/switch.md): boolean toggle for settings.
- [TabButtons](references/components/tabbuttons.md): segmented control with radiogroup semantics, for a filter, setting or form field.
- [Tabs](references/components/tabs.md): full content tabs.
- [Textarea](references/components/textarea.md): multi-line text input.
- [TextInput](references/components/textinput.md): single-line text input.
- [TimePicker](references/components/timepicker.md): time input.
- [Toast](references/components/toast.md): imperative notifications: `toast.success/error/warning/info`.
- [Tooltip](references/components/tooltip.md): hover hint.
- [Tree](references/components/tree.md): hierarchical list.

## Charts

Import chart components and chrome from `frappe-ui/charts`. Overview, Colors, States, and Dashboard are guides, not exports.

- [Overview](references/charts/overview.md): the shared prop model and how a chart reads its data.
- [Colors](references/charts/colors.md): the three `--chart-*` color ramps, `palette`, and the plot-area variables.
- [BarChart](references/charts/barchart.md): bars for comparing categories, or a measure over time.
- [LineChart](references/charts/linechart.md): lines for a measure over time.
- [AreaChart](references/charts/areachart.md): the line family with a fill.
- [DonutChart](references/charts/donutchart.md): share of a total.
- [PercentageBarChart](references/charts/percentagebarchart.md): share of a total as one bar, for a strip under a number.
- [FunnelChart](references/charts/funnelchart.md): stage-to-stage drop-off.
- [HeatmapChart](references/charts/heatmapchart.md): magnitude across two dimensions.
- [CalendarHeatmapChart](references/charts/calendarheatmapchart.md): a value per day, weeks as columns.
- [ScatterChart](references/charts/scatterchart.md): two measures against each other.
- [SankeyChart](references/charts/sankeychart.md): flow between two sets of nodes.
- [NumberCard](references/charts/numbercard.md): the KPI tile: one reading plus the change against a comparison period. Required `title` and `value`.
- [ChartCard](references/charts/chartcard.md): the card surface a chart sits on, for a plot you draw yourself.
- [ChartContainer](references/charts/chartcontainer.md): everything around the plot: title block, axis titles, states, legend row.
- [ChartLegend](references/charts/chartlegend.md): the series row under a plot. Bar, Line, Area, Donut, PercentageBar and Scatter draw it themselves.
- [ChartTooltip](references/charts/charttooltip.md): the reading beside the pointer, as HTML rather than a canvas overlay.
- [States](references/charts/states.md): the shared loading, error and empty states.
- [Dashboard](references/charts/dashboard.md): composing charts into a page. The page owns the grid, the charts own everything inside.

## Molecules

Each family has a separate entry point; see [INDEX.md](INDEX.md) for import paths.

- [Editor](references/molecules/editor.md): TipTap-based rich editor, from the `frappe-ui/editor` subpath (heavy, and not SSR-safe, so use only when needed).
- [CodeEditor](references/molecules/code-editor.md): CodeMirror 6 family, from the `frappe-ui/code-editor` subpath: the `useCodeEditor` engine, a renderless `CodeEditor`, `CodeEditorContent`, the `CodeKit` bundle, and `loadLanguage` for the ten optional `@codemirror/lang-*` peers. Renderless: the label, the field chrome and the expand affordance belong to the app.
- [List](references/molecules/list.md): the list family, from the `frappe-ui/list` subpath: `List`, `ListHeader`, `ListHeaderCell`, `ListHeaderCellSort`, `ListRows`, `ListRow`, `ListCell`, `ListGroup`. Feed or table mode.

## Data fetching

Import the `use*` composables from `frappe-ui`. See [DATA.md](DATA.md) for return shapes and write patterns.

- [useCall](references/data-fetching/use-call.md): invoke a whitelisted Frappe method; use `submit()` for an explicit write.
- [useDoc](references/data-fetching/use-doc.md): fetches and keeps one document reactive, shared with `useList` for the same `doctype`/`name`. Returns `doc`, not `data`.
- [useList](references/data-fetching/use-list.md): paginated, filterable list for a doctype, with `insert`/`setValue`/`delete` write helpers. `filters` and `orderBy` accept a ref or getter and refetch when it changes.
- [useDoctype](references/data-fetching/use-doctype.md): write-only helpers for a doctype (`insert`, `delete`, `setValue`, `runDocMethod`, `runMethod`) with no read of its own.
- [useNewDoc](references/data-fetching/use-new-doc.md): a reactive draft for a new document, submitted as an insert.

## Resources

Import the `create*Resource` factories from `frappe-ui`. See [DATA.md → Resources](DATA.md#resources) for differences from `use*`.

- [Resource](references/data-fetching/resource.md): `createResource`, base data fetcher.
- [List resource](references/data-fetching/list-resource.md): `createListResource`, paginated list.
- [Document resource](references/data-fetching/document-resource.md): `createDocumentResource`, single doc.

## Other

| Topic | Where to start |
|---|---|
| [Icons](references/other/icons.md) | Use a CSS icon class such as `lucide-plus` on a decorative `aria-hidden` span. Use `Icon` when the glyph may be an emoji or a component. |
| [Utilities](references/other/utilities.md) | Import `debounce` for delayed input handling, `dayjsLocal` for Frappe server timestamps, and `call` for a direct Frappe request without reactive state. |
| [Composables](references/other/composables.md) | Import from `frappe-ui`: use `useColorScheme()` for persisted theme preference, `useKeyboardShortcut()` for a registered shortcut, and `usePageMeta()` for the page title. |
| [Directives](references/other/directives.md) | Import `vFocus` in `<script setup>` and put `v-focus:autoselect` on a wrapper to focus its first focusable child and select its text. Use `v-on-outside-click="handler"` with the `vOnOutsideClick` import for custom surfaces; built-in overlays already handle outside clicks. |
| [Vite Plugin](references/other/vite.md) | Import `frappeui` from `frappe-ui/vite` in Vite config. Keep it in standalone apps, but disable the three Frappe-only options in [SETUP.md](SETUP.md). |
| [VitePress theme](references/other/vitepress-theme.md) | Import `defineDocsConfig` from `frappe-ui/vitepress` in `.vitepress/config.ts` and `theme` from the same subpath in `.vitepress/theme/index.ts`. |

## Experimental

Import from `frappe-ui/experimental` and verify against the installed version. Prefer stable `List` from `frappe-ui/list` for new lists.

- [Overview](references/experimental.md): what is parked, what is incubating, and why.
- [Accordion](references/experimental/accordion.md): sections behind labelled headers that expand and collapse.
- [Calendar](references/experimental/calendar.md): date and event view with Month, Week and Day modes.
- [CommandPalette](references/experimental/commandpalette.md): searchable command list in a dialog, composed from `CommandPaletteInput`, `CommandPaletteList`, `CommandPaletteGroup`, `CommandPaletteItem`, `CommandPaletteEmpty` and `CommandPaletteFooter`.
- [ListView](references/experimental/listview.md): the parked v0 list family. New code uses `frappe-ui/list`.
- [Stepper](references/experimental/stepper.md): ordered progress through a wizard, including optional substeps.
- [ThemeSwitcher](references/experimental/themeswitcher.md): parked theme picker. Prefer a `Select` with `useColorScheme()` for new code.
