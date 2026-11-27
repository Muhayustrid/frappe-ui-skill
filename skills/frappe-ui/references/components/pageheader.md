<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/PageHeader/PageHeader.md -->

# PageHeader

The header row at the top of a page in an app shell. On mobile, use
`PageHeaderMobile`.

```vue
<script setup lang="ts">
import { Breadcrumbs, Button, PageHeader } from 'frappe-ui'
</script>

<template>
  <div
    class="w-full overflow-hidden border-t border-l border-r bg-surface-base"
  >
    <PageHeader>
      <Breadcrumbs
        :items="[
          { label: 'Spaces', route: '#' },
          { label: 'Engineering', route: '#' },
        ]"
      />
      <Button label="New discussion" variant="solid" />
    </PageHeader>
  </div>
</template>
```

## Anatomy

`DesktopShell` and `MobileShell` each render a `PageHeaderTarget`. A page
declares its header anywhere in its template, and the header moves into that
target. `PageHeader` is the desktop header, `PageHeaderMobile` the mobile one,
and `PageHeaderBase` is the plain `<header>` both are built on.

```vue
<!-- Desktop page -->
<PageHeader>
  <PageHeaderTitle title="Engineering" />
  <Button label="New discussion" variant="solid" />
</PageHeader>

<!-- Mobile page -->
<PageHeaderMobile>
  <template #prefix>
    <PageHeaderBackButton :fallback-route="{ name: 'Spaces' }" />
  </template>
  <PageHeaderMobileTitle title="Engineering" />
  <template #suffix>
    <Button variant="ghost" icon="lucide-more-horizontal" label="Options" />
  </template>
</PageHeaderMobile>
```

## Examples

### Mobile header with a back button

`PageHeaderMobile` with a back button in `#prefix`, a long
`PageHeaderMobileTitle` in the default slot and an options button in `#suffix`.

```vue
<script setup lang="ts">
import { Button, PageHeaderMobile, PageHeaderMobileTitle } from 'frappe-ui'
</script>

<template>
  <div class="w-80 overflow-hidden border border-b-0 bg-surface-base">
    <PageHeaderMobile>
      <template #prefix>
        <Button
          variant="ghost"
          size="md"
          icon="lucide-chevron-left rtl:rotate-180"
          label="Back"
        />
      </template>
      <PageHeaderMobileTitle
        title="A discussion title that is long enough to need centering"
      />
      <template #suffix>
        <Button variant="ghost" icon="lucide-more-horizontal" label="Options" />
      </template>
    </PageHeaderMobile>
  </div>
</template>
```

## Behavior

### Where the header renders

The header moves into the `PageHeaderTarget` of the shell it is inside. A
header outside any shell uses the most recently mounted target. With no target
mounted, it renders where it is declared.

### Desktop header

`PageHeader` lays out its default slot in one row, with the items spread to
both ends. It is at least 48px tall. `PageHeaderTitle` shows `title`, or its
default slot, as the page title.

### Click to scroll to the top

A single click on the header's empty area scrolls the page to the top. Clicks
on interactive elements are ignored: links, buttons, form controls, labels, and
anything with `role="button"`. To exclude another element, add
`data-no-scroll-top`:

```vue
<PageHeader>
  <Breadcrumbs data-no-scroll-top :items="items" />
</PageHeader>
```

### Mobile header

`PageHeaderMobile` keeps its title centered however wide the `#prefix` and
`#suffix` controls are. The title stays on one line and ends with an ellipsis.
Pass it as the `title` prop, or put a `PageHeaderMobileTitle` in the default
slot. `PageHeaderMobileTitle` takes a `#prefix` for an icon beside the text,
and only the text is shortened.

The header is 52px tall, fixed. For a header of another height, use
`PageHeaderBase` with your own class.

### Back button

`PageHeaderBackButton` goes back through history, the way the browser's back
button does. It navigates to `fallbackRoute` only when there is no in-app
history to go back to, for example after a cold load onto a deep link. Give it
a fallback so the page has a way out on a cold load.

### Custom header

`PageHeaderBase` is the unstyled `<header>` behind both headers. Use it for a
custom strip, such as a toolbar or a second row, that renders into the same
target.

## Accessibility

Each header renders a `<header>` element. `PageHeaderMobile` puts its title in
an `<h1>`. `PageHeaderBackButton` is named "Back". Set `label` to change it.

## API reference

```ts
const pageHeaderSlots = [
  {
    name: 'default',
    description: 'The header\'s contents — title, breadcrumbs, actions.',
    type: 'any'
  }
]

  const pageHeaderBaseSlots = [
  {
    name: 'default',
    description: 'The header\'s contents.',
    type: 'any'
  }
]

  const pageHeaderTitleProps = [
  {
    name: 'title',
    description: 'Title text. Overridden by the default slot.',
    required: false,
    type: 'string'
  }
]

  const pageHeaderTitleSlots = [
  {
    name: 'default',
    description: 'Overrides `title` with custom markup.',
    type: 'any'
  }
]

  const pageHeaderMobileProps = [
  {
    name: 'title',
    description: 'Title shown centered in the header. Overridden by the default slot.',
    required: false,
    type: 'string'
  }
]

  const pageHeaderMobileSlots = [
  {
    name: 'prefix',
    description: 'A control leading the centered title — usually a `PageHeaderBackButton`.',
    type: 'any'
  },
  {
    name: 'default',
    description: 'The centered title. Overrides `title`; usually a `PageHeaderMobileTitle`.',
    type: 'any'
  },
  {
    name: 'suffix',
    description: 'A control trailing the centered title — usually an action button.',
    type: 'any'
  }
]

  const pageHeaderMobileTitleProps = [
  {
    name: 'title',
    description: 'Title text. Overridden by the default slot.',
    required: false,
    type: 'string'
  }
]

  const pageHeaderMobileTitleSlots = [
  {
    name: 'prefix',
    description: 'A leading glyph next to the title text — keeps its natural size while the text truncates.',
    type: 'any'
  },
  {
    name: 'default',
    description: 'Overrides `title` with custom markup.',
    type: 'any'
  }
]

  const pageHeaderBackButtonProps = [
  {
    name: 'fallbackRoute',
    description: 'Fallback destination, used only when there is no in-app history to go back to\n(a cold load onto a deep link). Taps otherwise go back through history.',
    required: false,
    type: 'RouteDestination'
  },
  {
    name: 'label',
    description: 'Accessible label for the button.',
    required: false,
    type: 'string',
    default: '"Back"'
  }
]
```
