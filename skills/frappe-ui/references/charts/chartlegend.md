<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/charts/docs/ChartLegend.md -->

# ChartLegend

The row of series names under a plot. Each entry shows a series name and its
color, and hides or shows the series when pressed.

```js
import { ChartLegend } from 'frappe-ui/charts'
```

It holds no state. `items` is its only input, and its two events report what
the user did. The chart that owns the series decides what to do. Put it in the
`#legend` slot of `ChartContainer`.



## Entries

One `ChartLegendItem` per series, in the order the series are drawn:

| Field | What it is |
| --- | --- |
| `name` | Series identity, i.e. what `change` and `highlight` report. |
| `label` | The name as it should read. |
| `color` | The color the series is drawn in. |
| `hidden` | Whether the series is hidden. |
| `hint` | A muted note after the label, e.g. a donut slice's share. |
| `weight` | What the series is worth across the chart. Ranks the entries that do not fit. |
| `formattedValue` | A number printed beside the entry in the "+N more" list. |

A hidden entry keeps its place in the row and dims: the swatch fades and the
label turns gray. If the entry left the row, there would be no way to show the
series again.

## Many series

The legend takes one row at most. When every entry fits, the row shows them in
the order the series are drawn. When they do not, the row shows the entries
with the largest `weight`, as many as fit, and then a "+N more" button. A legend
that wrapped would take the plot's height, and one that scrolled would hide
series names that most users never scroll to.

"+N more" opens a [MultiSelect](../components/multiselect.md) list of every
entry, largest `weight` first, with the drawn series checked. Checking or
unchecking a row emits `change` the way pressing an entry on the row does. The
arrow keys move through the list and Enter toggles a row. Past 15 entries, the
list has a search field, and matches keep the same order. The rows do not emit
`highlight`: only the entries on the row do.

An entry with no `weight` keeps its given place after the ranked ones, and so
does "Others". The line, area and bar charts weigh a series by the sum of its
values' magnitudes, which is also what `maxSeries` keeps, and print no number:
a sum is wrong for a rate or an average. `DonutChart` weighs a slice by its
value and prints it, since slices are parts of a whole. `ScatterChart` passes
no weight, so its groups keep their order.



## Toggling

`change` emits the `name` of the pressed entry. Toggle that series in your own
hidden list and pass the new `items` back.

Every entry is a button. The visible text is the series label, but the
accessible name says what pressing it does ("Hide Signups" or "Show Signups").
`aria-pressed` holds the current state.

## Highlighting

`highlight` emits the `name` of the entry under the pointer or with focus, and
`null` when it leaves. Use it to emphasize that series in the plot and dim the
rest. It fires on both hover and focus, so keyboard users get the same effect as
mouse users.

## API reference

```ts
const propsData = [
  {
    name: 'items',
    description: 'One entry per series, in the order they are drawn. Entries that do not fit\non one row go behind "+N more", ranked by `weight`.',
    required: true,
    type: 'ChartLegendItem[]'
  }
]

  const emitsData = [
  {
    name: 'change',
    description: 'An entry was pressed: the named series\' visibility flipped.',
    type: '[name: string]'
  },
  {
    name: 'highlight',
    description: 'The highlighted series, or null when the highlight clears.',
    type: '[name: string | null]'
  }
]
```
