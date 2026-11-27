<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/charts/docs/NumberCard.md -->

# NumberCard

Shows one number and how it changed since a comparison period.

## A number and its change

`negativeIsBetter` swaps the change colors for metrics like churn, where down is
good. `format` formats the value and the target. A `null` value shows the empty
state, because a missing number is not zero. The card shows the period it
compares against, but your app chooses that period, so put the control for it in
the `#caption` slot.



## Sparklines

`sparkline` adds a small trend line across the bottom of the card, with no
axes. `type` sets how it draws, as on other charts. `area` (the default) suits a
continuous value, `line` draws the line with no fill, and `bar` suits a value
counted per period.



`color` sets the color of the number. Use it when the card stands for a series
drawn in that color elsewhere on the page. The storage card above uses the color
of its trend line. It works like `seriesConfig[key].color` for a series. It does
not change the rest of the card, and the change keeps the color that shows
whether the number went up or down.

## Progress

`progress` draws a bar under the number and its change for how far the number has come towards a whole, such as disk used or revenue against a target. Pass `value` as a percent, the way `Progress` takes it. Past 100 the bar fills and a gap marks where 100 falls. The bar fills in the same blue as a sparkline, and `color` overrides it.



## The title row

`#actions` goes at the end of the row and `#title-suffix` right after the title
text. Neither makes the row taller: both are centered on the title, so the
number does not move. The card's title is smaller than a chart's, and
`#title-suffix` uses the title's font size, so an icon sized in `em` is smaller
here than on a `ChartContainer`.

## API reference

```ts
const propsData = [
  {
    name: 'title',
    description: 'Heads the card. Left out, the chart draws no header row at all.\nWhat the reading is, printed above the number.',
    required: true,
    type: 'string'
  },
  {
    name: 'dir',
    description: 'Forces layout direction; defaults to document.documentElement.dir',
    required: false,
    type: 'ChartDir'
  },
  {
    name: 'loading',
    description: 'Draws the placeholder in place of the plot, for data still on its way.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'error',
    description: 'Puts the chart in its error state and prints this message under it. Data\nthe chart cannot draw shows the empty state, not this one.',
    required: false,
    type: 'string | null'
  },
  {
    name: 'value',
    description: 'A string renders as given: the formatting props only apply to a number.',
    required: true,
    type: 'number | string | null'
  },
  {
    name: 'color',
    description: 'Ink the reading is printed in, e.g. the color of the series it summarizes\non a dashboard. One color for one mark, the way `SeriesStyle.color` names\na series\' own — it does not restyle the card, and the delta keeps the\ntone that says which way the number moved.',
    required: false,
    type: 'string'
  },
  {
    name: 'prefix',
    description: 'Printed before the number, e.g. a currency sign.',
    required: false,
    type: 'string'
  },
  {
    name: 'suffix',
    description: 'Printed after the number, e.g. a unit.',
    required: false,
    type: 'string'
  },
  {
    name: 'target',
    description: 'What the reading is measured against, printed as a muted ` / target` in the value\'s own formatting.',
    required: false,
    type: 'number | string'
  },
  {
    name: 'delta',
    description: 'Change against the comparison period. Sign drives the arrow.',
    required: false,
    type: 'number | null'
  },
  {
    name: 'deltaPrefix',
    description: 'Unit printed before the delta, e.g. `\'$\'`.',
    required: false,
    type: 'string'
  },
  {
    name: 'deltaSuffix',
    description: 'Unit printed after the delta, e.g. `\'%\'`.',
    required: false,
    type: 'string'
  },
  {
    name: 'deltaCaption',
    description: 'What the delta is measured against, e.g. `\'vs last month\'`.',
    required: false,
    type: 'string'
  },
  {
    name: 'negativeIsBetter',
    description: 'Flips the delta colors, for metrics like churn or cost.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'format',
    description: 'Prints the reading and the target: they are one measure, read against each other.',
    required: false,
    type: 'ChartValueFormatter'
  },
  {
    name: 'deltaFormat',
    description: 'Prints the delta. It takes the absolute value: the arrow beside it carries the sign.',
    required: false,
    type: 'ChartValueFormatter'
  },
  {
    name: 'sparkline',
    description: 'A trend across the bottom of the card: shape only, no axes to read against.',
    required: false,
    type: 'NumberCardSparkline'
  },
  {
    name: 'progress',
    description: 'How far the reading has come towards a whole, drawn as a bar under it and its change.',
    required: false,
    type: 'NumberCardProgress'
  },
  {
    name: 'card',
    description: 'Draws the card surface: border, background, corner radius and padding. On\nby default. Set it to `false` for a chart the app has already placed inside\na card of its own, so a bordered box does not nest in a bordered box.',
    required: false,
    type: 'boolean',
    default: 'true'
  }
]

  const slotsData = [
  {
    name: 'loading',
    description: 'Replaces the whole placeholder, e.g. with a skeleton of the app\'s own.',
    type: 'any'
  },
  {
    name: 'error',
    description: 'Replaces the message, e.g. to put a retry button beside it.',
    type: '{ error?: string | null | undefined; }'
  },
  {
    name: 'empty',
    description: 'Replaces the "no data" line, e.g. with a hint about the filters.',
    type: 'any'
  },
  {
    name: 'actions',
    description: '',
    type: 'any'
  },
  {
    name: 'title-suffix',
    description: 'A mark right after the title, on the same line. The title truncates\naround it and it keeps its width. It renders in the title\'s font size, so\ncontent sized in `em` is smaller on a NumberCard than on a chart.',
    type: 'any'
  },
  {
    name: 'caption',
    description: 'Replaces `deltaCaption`, e.g. with a Dropdown that changes the period.',
    type: '{ caption?: string | undefined; }'
  }
]
```
