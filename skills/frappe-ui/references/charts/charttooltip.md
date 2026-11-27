<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/charts/docs/ChartTooltip.md -->

# ChartTooltip

The values shown beside the pointer. It is HTML, not drawn on the canvas, so
it uses the app's fonts, colors and shadows, and a slot can replace its content.

```js
import { ChartTooltip } from 'frappe-ui/charts'
```

Every built-in chart uses this tooltip. Use it directly when you draw the plot
yourself. On a built-in chart, use the `#tooltip` slot to replace its content.



## Showing it

The tooltip does not track the pointer itself. The plot tells it what to show
and where:

- `open` shows it. It draws nothing while `open` is false, or when `items` is
  empty.
- `x` and `y` are viewport coordinates, i.e. `clientX` and `clientY`.
- `label` heads the rows, e.g. the category or the date the pointer is over.
- `items` are the rows.
- `rows` are the data rows behind the values, passed on to the slot. One row
  for a point, cell, band or stage, every grouped row for a donut's "Others"
  slice, and none for a total that does not come from one row.
- `dir` sets the layout direction. It defaults to
  `document.documentElement.dir`.

## Rows

One `ChartTooltipItem` per row:

| Field | What it is |
| --- | --- |
| `name` | Series identity. |
| `label` | The series name as it should read. |
| `color` | The color the series is drawn in, shown as a swatch. |
| `value` | The raw number. |
| `formattedValue` | That number as it should print, e.g. through `format`. |
| `percent` | Share of the total, printed after the value. Part-to-whole charts only. |
| `kind` | `'series'` for a value the plot draws, `'context'` for one it does not. A `'context'` item has no color and prints below a divider. |

The tooltip prints `formattedValue`. `value` is for a slot that needs to do its
own math.

## Placement

The tooltip is teleported out of the chart and positioned against the viewport,
so a card that clips its content cannot clip the tooltip.

It sits 12px past the pointer, and flips to the other side of it when that would
run off the right edge or the bottom. Whatever is left is clamped to 4px inside
the window, so a tooltip near a corner still fits.

The size is measured after render, because slot content can be any size. The
tooltip stays hidden for the one frame the measurement takes, so it never shows
at the previous position first.

## Replacing the content

The default slot replaces the whole body, with `label`, `items` and `rows` as
slot props. Use it for content the rows cannot show, such as a sparkline, a
second measure or a link to the records behind the point.

```vue
<ChartTooltip :open="open" :x="x" :y="y" :label="label" :items="items">
  <template #default="{ label, items }">
    <div class="text-p-sm text-ink-gray-5">{{ label }}</div>
    <div v-for="item in items" :key="item.name" class="text-p-sm">
      {{ item.label }}: {{ item.formattedValue }}
    </div>
  </template>
</ChartTooltip>
```

## API reference

```ts
const propsData = [
  {
    name: 'open',
    description: 'Draws the tooltip. It is measured before it is placed, so it flips at the\nviewport edge rather than running off it.',
    required: true,
    type: 'boolean'
  },
  {
    name: 'x',
    description: 'Viewport x of the point the tooltip hangs off, i.e. the pointer.',
    required: true,
    type: 'number'
  },
  {
    name: 'y',
    description: 'Viewport y of the point the tooltip hangs off, i.e. the pointer.',
    required: true,
    type: 'number'
  },
  {
    name: 'label',
    description: 'Heads the tooltip, e.g. the category the readings below it belong to.',
    required: false,
    type: 'string'
  },
  {
    name: 'items',
    description: 'One row per reading, in the order they should be read.',
    required: true,
    type: 'ChartTooltipItem[]'
  },
  {
    name: 'rows',
    description: 'The rows behind the reading, handed to the slot so a body can read a column\nthe plot never drew. Empty when the pointer is over an aggregate that\nstands for no single row.',
    required: true,
    type: 'Record<string, any>[]'
  },
  {
    name: 'dir',
    description: 'Forces layout direction; defaults to document.documentElement.dir',
    required: false,
    type: 'ChartDir'
  }
]

  const slotsData = [
  {
    name: 'default',
    description: 'Replaces the whole tooltip body, headline row included.',
    type: 'ChartTooltipSlotProps'
  }
]
```
