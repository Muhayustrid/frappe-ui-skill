<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/Tree/Tree.md -->

# Tree

Shows nested data as rows that open and close, such as folders or an org
chart. It supports keyboard navigation and, when you turn it on, drag and drop
to move nodes.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Tree } from 'frappe-ui'
import type { TreeNode } from '../types'

const nodes = ref<TreeNode[]>([
  {
    name: 'guest',
    label: 'Guest',
    children: [
      {
        name: 'downloads',
        label: 'Downloads',
        children: [
          {
            name: 'download.zip',
            label: 'download.zip',
            children: [{ name: 'image.png', label: 'image.png' }],
          },
        ],
      },
      {
        name: 'documents',
        label: 'Documents',
        children: [
          { name: 'somefile.txt', label: 'somefile.txt' },
          { name: 'somefile.pdf', label: 'somefile.pdf' },
        ],
      },
    ],
  },
])

// Expansion is a list of node keys. A key that is absent stays collapsed, so
// `Documents` starts closed and the tree writes nothing back to `nodes`.
const expanded = ref(['guest', 'downloads'])
</script>

<template>
  <div class="w-80">
    <Tree :nodes="nodes" node-key="name" v-model:expanded="expanded" />
  </div>
</template>
```

## Examples

### Expand and collapse all

Buttons above the tree call `expandAll()`, `collapseAll()` and `toggle(key)`
on a template ref. They write the same `expanded` model that clicking a row
does.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Button, Tree } from 'frappe-ui'
import type { TreeExposed, TreeNode } from '../types'

const nodes = ref<TreeNode[]>([
  {
    name: 'guest',
    label: 'Guest',
    children: [
      {
        name: 'downloads',
        label: 'Downloads',
        children: [
          {
            name: 'download.zip',
            label: 'download.zip',
            children: [{ name: 'image.png', label: 'image.png' }],
          },
        ],
      },
      {
        name: 'documents',
        label: 'Documents',
        children: [
          { name: 'somefile.txt', label: 'somefile.txt' },
          { name: 'somefile.pdf', label: 'somefile.pdf' },
        ],
      },
    ],
  },
])

// The model holds the keys of the open nodes, so it doubles as a readout.
const expanded = ref<string[]>(['guest'])

// `expandAll` / `collapseAll` write the same model through a template ref.
const tree = ref<TreeExposed | null>(null)
</script>

<template>
  <div class="flex flex-col gap-3">
    <div class="flex gap-2">
      <Button @click="tree?.expandAll()">Expand all</Button>
      <Button @click="tree?.collapseAll()">Collapse all</Button>
      <Button @click="tree?.toggle('documents')">Toggle Documents</Button>
    </div>
    <div class="w-80">
      <Tree
        ref="tree"
        :nodes="nodes"
        node-key="name"
        v-model:expanded="expanded"
      />
    </div>
    <p class="text-sm text-ink-gray-5">
      expanded: {{ expanded.length ? expanded.join(', ') : '(none)' }}
    </p>
  </div>
</template>
```

### Folder outline

`guides` sets the lines that link parents to children: `connectors`, `lines`
or `none`.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { TabButtons, Tree } from 'frappe-ui'
import type { TreeNode } from '../types'

const nodes = ref<TreeNode[]>([
  {
    name: 'guest',
    label: 'Guest',
    children: [
      {
        name: 'downloads',
        label: 'Downloads',
        children: [
          {
            name: 'download.zip',
            label: 'download.zip',
            children: [{ name: 'image.png', label: 'image.png' }],
          },
        ],
      },
      {
        name: 'documents',
        label: 'Documents',
        children: [
          { name: 'somefile.txt', label: 'somefile.txt' },
          { name: 'somefile.pdf', label: 'somefile.pdf' },
        ],
      },
    ],
  },
])

const expanded = ref(['guest', 'downloads', 'download.zip', 'documents'])

const guides = ref<'connectors' | 'lines' | 'none'>('connectors')
const guideOptions = [
  { label: 'Connectors', value: 'connectors' },
  { label: 'Lines', value: 'lines' },
  { label: 'None', value: 'none' },
]
</script>

<template>
  <div class="flex flex-col gap-4">
    <TabButtons v-model="guides" :options="guideOptions" />
    <div class="w-80">
      <Tree
        :nodes="nodes"
        node-key="name"
        :guides="guides"
        v-model:expanded="expanded"
      />
    </div>
  </div>
</template>
```

### Move files between folders

`draggable` lets people drag a file into a folder or between siblings. The
`move` function allows drops only into folders, and `@drag-end` saves the
move to the data. Turn on **Disable interaction** to see `disabled` stop both
dragging and opening rows.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Switch, Tree } from 'frappe-ui'
import type { DropInfo, MoveContext, TreeNode } from '../types'

const nodes = ref<TreeNode[]>([
  {
    name: 'guest',
    label: 'Guest',
    children: [
      {
        name: 'downloads',
        label: 'Downloads',
        children: [
          {
            name: 'archive',
            label: 'archive',
            children: [{ name: 'image.png', label: 'image.png' }],
          },
        ],
      },
      {
        name: 'documents',
        label: 'Documents',
        children: [
          { name: 'resume.pdf', label: 'resume.pdf' },
          { name: 'notes.txt', label: 'notes.txt' },
        ],
      },
    ],
  },
])

const disabled = ref(false)
const expanded = ref(['guest', 'downloads', 'archive', 'documents'])

// Folders (nodes with a `children` array) can receive drops; files cannot.
function move({ target, position }: MoveContext) {
  if (position === 'inside') return Array.isArray(target.children)
  return true
}

// Locate a node by key, returning the array that holds it and its index.
function locate(
  list: TreeNode[],
  key: string,
): { list: TreeNode[]; index: number } | null {
  for (let i = 0; i < list.length; i++) {
    if (list[i].name === key) return { list, index: i }
    const children = list[i].children
    if (children) {
      const found = locate(children, key)
      if (found) return found
    }
  }
  return null
}

// Persist a committed move by splicing the node into its new position.
function onDragEnd(info: DropInfo | null) {
  if (!info) return
  const roots = nodes.value
  const src = locate(roots, info.node.name as string)
  if (!src) return
  const [moved] = src.list.splice(src.index, 1)

  let dest = roots
  if (info.to !== null) {
    const hit = locate(roots, info.to as string)
    const parent = hit?.list[hit.index]
    if (parent) {
      if (!parent.children) parent.children = []
      // Open the new parent so the moved node stays visible.
      if (!expanded.value.includes(info.to as string))
        expanded.value = [...expanded.value, info.to as string]
      dest = parent.children
    }
  }
  dest.splice(info.newIndex, 0, moved)
}
</script>

<template>
  <div class="flex flex-col gap-3">
    <Switch v-model="disabled" label="Disable interaction" />
    <div class="w-80">
      <Tree
        :nodes="nodes"
        node-key="name"
        v-model:expanded="expanded"
        :disabled="disabled"
        draggable
        :move="move"
        @drag-end="onDragEnd"
      />
    </div>
  </div>
</template>
```

### Reporting lines

The `#item-prefix`, `#item-label` and `#item-suffix` slots turn each row into
an avatar, a name with a role, and an add button. `guides="none"` removes the
lines.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { Avatar, Button, Tree } from 'frappe-ui'
import type { TreeNode } from '../types'

const nodes = ref<TreeNode[]>([
  {
    id: 'james',
    label: 'James Cooper',
    role: 'Head of Sales',
    image: 'https://i.pravatar.cc/80?img=11',
    children: [
      {
        id: 'wade',
        label: 'Wade Warren',
        role: 'Account Executive',
        image: 'https://i.pravatar.cc/80?img=12',
        children: [
          {
            id: 'ethan',
            label: 'Ethan Howard',
            role: 'Sales Rep',
            image: 'https://i.pravatar.cc/80?img=13',
          },
        ],
      },
      {
        id: 'cody',
        label: 'Cody Fisher',
        role: 'Account Executive',
        image: 'https://i.pravatar.cc/80?img=15',
      },
    ],
  },
])

const expanded = ref(['james', 'wade'])

function addReport(node: TreeNode) {
  console.log('add report under', node.id)
}
</script>

<template>
  <div class="w-96" style="--tree-row-height: 48px">
    <Tree
      :nodes="nodes"
      node-key="id"
      guides="none"
      v-model:expanded="expanded"
    >
      <!-- Avatar on the left -->
      <template #item-prefix="{ node }">
        <Avatar
          :image="node.image as string"
          :label="node.label as string"
          size="lg"
        />
      </template>

      <template #item-label="{ node }">
        <div class="flex min-w-0 flex-col">
          <span class="truncate text-base text-ink-gray-8">{{
            node.label
          }}</span>
          <span class="truncate text-sm text-ink-gray-5">{{ node.role }}</span>
        </div>
      </template>

      <!-- Add action -->
      <template #item-suffix="{ node }">
        <Button
          variant="ghost"
          icon="lucide-plus"
          :aria-label="`Add report under ${node.label}`"
          @click.stop="addReport(node)"
        />
      </template>
    </Tree>
  </div>
</template>
```

## Behavior

### Node shape

Pass the top-level nodes as `nodes`. Each node is a plain object with a
`label` and optional `children`. Its unique key lives in the field named by
`nodeKey`, such as `name`. A node with no `children`, or an empty array, is a
leaf. Extra fields reach the slots, so you can show avatars, roles or badges.

```vue
<script setup>
const nodes = ref([
  {
    name: 'src',
    label: 'src',
    children: [
      { name: 'index.ts', label: 'index.ts' },
      { name: 'app.vue', label: 'app.vue' },
    ],
  },
])
const expanded = ref(['src'])
</script>

<template>
  <Tree :nodes="nodes" node-key="name" v-model:expanded="expanded" />
</template>
```

To show a field other than `label`, use the `#item-label` slot rather than
renaming the field. The tree never writes to the objects you pass in.

### Expansion

`v-model:expanded` holds the keys of the open nodes. A missing key means
closed, so a tree with no bound model shows only its top-level nodes.
Clicking a row, or pressing `Enter` or `Space` on it, opens or closes it.

Every change assigns a new array, so shallow watchers, immutable stores and
undo logs see it.

### Drag and drop

Set `draggable` to turn on dragging. The tree works out the drop position from
the pointer (`before`, `inside` or `after`) and shows where the node will land.

- `move(ctx)` runs while you hover a target. Return `false` to reject it: the
  pointer shows no-drop and the marker hides. The tree already rejects a drop
  on the node itself or into its own children, so `move` only needs your own
  rules. `ctx` is `{ node, target, position }`.
- `@drag-start` fires with the node when a drag starts.
- `@drag-end` fires when the drag ends, with a `DropInfo` for a finished move
  or `null` for a cancelled one. Apply the move to your data and update
  `nodes`.

```vue
<Tree
  :nodes="nodes"
  node-key="name"
  draggable
  :move="({ target }) => Array.isArray(target.children)"
  @drag-end="onDragEnd"
/>
```

`DropInfo` is `{ node, from, to, position, oldIndex, newIndex }`. `from` and
`to` are the keys of the old and new parents, `null` at the top level.
`newIndex` is the node's final index in its new parent, counted after it was
removed from the old one.

### Disabled

`disabled` stops people from opening, closing and dragging rows.

### Custom rows

`#item-prefix`, `#item-label` and `#item-suffix` fill parts of the default
row. `#item` replaces the whole row and receives `toggle` along with the
row's state.

## Accessibility

The tree follows the WAI-ARIA tree view pattern, with `role="tree"`,
`treeitem` and `group`. Each row reports its level, its position among its
siblings and, for a parent, whether it is open.

| Keys              | Action                                        |
| ----------------- | --------------------------------------------- |
| `ArrowDown` / `ArrowUp`         | Move to the next or previous visible row      |
| `ArrowRight`               | Open the row, or move to its first child      |
| `ArrowLeft`               | Close the row, or move to its parent          |
| `Home` / `End`    | Move to the first or last row                 |
| `Enter` / `Space` | Open or close the row                         |
| Letters           | Move to the next row whose label starts with them |

In a right-to-left layout `ArrowLeft` and `ArrowRight` swap.

Drag and drop announces "Picked up", "Moved" and "Cancelled move" to screen
readers.

## API reference

```ts
const propsData = [
  {
    name: 'nodes',
    description: 'Forest roots to render. Each node may contain nested children to form the\ntree structure.',
    required: true,
    type: 'TreeNode[]'
  },
  {
    name: 'nodeKey',
    description: 'Name of the field that uniquely identifies each node.',
    required: false,
    type: 'string',
    default: '"key"'
  },
  {
    name: 'draggable',
    description: 'Enable drag-and-drop. Nodes can be dragged onto one another to reparent, or\nbetween siblings to reorder.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'move',
    description: 'Gate a drop while dragging. Receives the live drag context and returns\nwhether the drop is allowed — a rejected target shows the no-drop cursor and\nhides the drop indicator. Built-in guards (drop-on-self, drop-into-own-\ndescendant) run first, so this only carries your domain rules.',
    required: false,
    type: '((ctx: MoveContext) => boolean)'
  },
  {
    name: 'guides',
    description: 'Visual style of the indentation guides.',
    required: false,
    type: '"connectors" | "lines" | "none"',
    default: '"connectors"'
  },
  {
    name: 'disabled',
    description: 'Disable all interaction — expand/collapse and drag.',
    required: false,
    type: 'boolean',
    default: 'false'
  },
  {
    name: 'expanded',
    description: 'Keys of the expanded nodes. Two-way via `v-model:expanded` — the tree\nreplaces the array as rows toggle and never writes to your nodes. A key\nthat is absent means that node is collapsed, so the default renders only\nthe roots.',
    required: false,
    type: 'TreeKey[]',
    default: '[]'
  }
]

  const slotsData = [
  {
    name: 'item',
    description: 'Fully replaces a row\'s default rendering — receives `toggle` plus node state.',
    type: 'TreeNodeSlotProps'
  },
  {
    name: 'item-prefix',
    description: 'Leading content before the label, inside the default row layout.',
    type: 'Omit<TreeNodeSlotProps, "toggle">'
  },
  {
    name: 'item-label',
    description: 'Overrides the row\'s label text, inside the default row layout.',
    type: 'Omit<TreeNodeSlotProps, "toggle">'
  },
  {
    name: 'item-suffix',
    description: 'Trailing content after the label, inside the default row layout.',
    type: 'Omit<TreeNodeSlotProps, "toggle">'
  },
  {
    name: 'empty',
    description: 'Rendered instead of the tree when `nodes` is empty.',
    type: 'any'
  }
]

  const emitsData = [
  {
    name: 'update:expanded',
    description: 'Fired when the expanded changes.',
    type: '[value: TreeKey[]]'
  },
  {
    name: 'drag-start',
    description: 'Fired when a drag is picked up.',
    type: '[node: TreeNode]'
  },
  {
    name: 'drag-end',
    description: 'Fired when a drag ends: the committed move, or `null` if it was cancelled.',
    type: '[info: DropInfo | null]'
  }
]

  const exposedData = [
  {
    name: 'expand',
    description: 'Opens the node with this key. A key whose children have not loaded yet\nopens as soon as they arrive. Like every method here, it works while the\ntree is `disabled`.',
    type: '(key: TreeKey) => void'
  },
  {
    name: 'collapse',
    description: 'Closes the node with this key.',
    type: '(key: TreeKey) => void'
  },
  {
    name: 'toggle',
    description: 'Flips the node with this key.',
    type: '(key: TreeKey) => void'
  },
  {
    name: 'expandAll',
    description: 'Opens every node that has children, keeping any keys already open.',
    type: '() => void'
  },
  {
    name: 'collapseAll',
    description: 'Closes every node.',
    type: '() => void'
  }
]
```
