<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/experimental/ListView/ListView.md -->

# ListView

Displays data in a structured, scrollable list with support for columns, groups, and custom content. Makes large sets of information easy to view, select, and interact with.

> **Parked** — `ListView` left the root export in `1.0.0` and now ships from
> [`frappe-ui/experimental`](../experimental.md), exempt from the deprecation
> policy. [`frappe-ui/list`](../molecules/list.md) is the replacement for new
> code, but it has no equivalent for ListView's config-driven columns yet, so
> `ListView` stays here until it does.

```ts
import { ListView } from 'frappe-ui/experimental'

// The parts a custom list composes:
import {
  ListHeader,
  ListHeaderItem,
  ListRows,
  ListRow,
  ListRowItem,
  ListSelectBanner,
} from 'frappe-ui/experimental'
```

## Simple List

```vue
<script setup>
import { h, reactive } from 'vue'

import { Avatar } from 'frappe-ui'
import { ListView } from '..'

const columns = reactive([
  {
    label: 'Name',
    key: 'name',
    width: 3,
    getLabel: ({ row }) => row.name,
    prefix: ({ row }) =>
      h(Avatar, { shape: 'circle', image: row.user_image, size: 'sm' }),
  },
  { label: 'Email', key: 'email', width: '200px' },
  { label: 'Role', key: 'role' },
  { label: 'Status', key: 'status' },
])

const rows = [
  {
    id: 1,
    name: 'John Doe',
    email: 'john@doe.com',
    status: 'Active',
    role: 'Developer',
    user_image: 'https://avatars.githubusercontent.com/u/499550',
  },
  {
    id: 2,
    name: 'Jane Doe',
    email: 'jane@doe.com',
    status: 'Inactive',
    role: 'HR',
    user_image: 'https://avatars.githubusercontent.com/u/499120',
  },
]
</script>

<template>
  <ListView
    class="h-[150px]"
    :columns="columns"
    :rows="rows"
    :options="{
      getRowRoute: (row) => `/users/${row.id}`,
      selectable: true,
      showTooltip: true,
      resizeColumn: true,
    }"
    row-key="id"
  />
</template>
```

## Custom List
```vue
<script setup>
import { reactive } from 'vue'

import { Avatar, Badge, Button } from 'frappe-ui'
import {
  ListHeader,
  ListHeaderItem,
  ListRow,
  ListRowItem,
  ListRows,
  ListSelectBanner,
  ListView,
} from '..'

const custom_columns = reactive([
  { label: 'Name', key: 'name', width: 3, icon: 'lucide-user' },
  { label: 'Email', key: 'email', width: '200px', icon: 'lucide-at-sign' },
  { label: 'Role', key: 'role', icon: 'lucide-users' },
  { label: 'Status', key: 'status', icon: 'lucide-check-circle' },
])

const custom_rows = [
  {
    id: 1,
    name: {
      label: 'John Doe',
      image: 'https://avatars.githubusercontent.com/u/499550',
    },
    email: 'john@doe.com',
    status: { label: 'Active', bg_color: 'bg-surface-green-3' },
    role: { label: 'Developer', color: 'green' },
  },
  {
    id: 2,
    name: {
      label: 'Jane Doe',
      image: 'https://avatars.githubusercontent.com/u/499120',
    },
    email: 'jane@doe.com',
    status: { label: 'Inactive', bg_color: 'bg-surface-red-7' },
    role: { label: 'HR', color: 'red' },
  },
]
</script>

<template>
  <ListView
    class="h-[150px]"
    :columns="custom_columns"
    :rows="custom_rows"
    :options="{
      onRowClick: (row) => console.log(row),
      selectable: true,
      showTooltip: true,
      resizeColumn: true,
    }"
    row-key="id"
  >
    <ListHeader>
      <ListHeaderItem
        v-for="column in custom_columns"
        :key="column.key"
        :item="column"
      >
        <template #prefix="{ item }">
          <span class="size-4" :class="item.icon" />
        </template>
      </ListHeaderItem>
    </ListHeader>
    <ListRows>
      <ListRow
        v-for="row in custom_rows"
        :key="row.id"
        v-slot="{ column, item }"
        :row="row"
      >
        <ListRowItem :item="item" :align="column.align">
          <template #prefix>
            <div
              v-if="column.key === 'status'"
              class="h-3 w-3 rounded-full"
              :class="item.bg_color"
            />
            <Avatar
              v-if="column.key === 'name'"
              shape="circle"
              :image="item.image"
              size="sm"
            />
          </template>
          <Badge
            v-if="column.key === 'role'"
            variant="subtle"
            :theme="item.color"
            size="md"
            :label="item.label"
          />
        </ListRowItem>
      </ListRow>
    </ListRows>
    <ListSelectBanner>
      <template #actions="{ unselectAll }">
        <div class="flex gap-2">
          <Button variant="ghost" label="Delete" />
          <Button variant="ghost" label="Unselect all" @click="unselectAll" />
        </div>
      </template>
    </ListSelectBanner>
  </ListView>
</template>
```

## Grouped Rows
```vue
<script setup>
import { reactive, ref } from 'vue'
import { ListView } from '..'

const group_columns = reactive([
  { label: 'Name', key: 'name', width: 3 },
  { label: 'Email', key: 'email', width: '200px' },
  { label: 'Role', key: 'role' },
  { label: 'Status', key: 'status' },
])

const grouped_rows = ref([
  {
    group: 'Developer',
    collapsed: false,
    rows: [
      {
        id: 2,
        name: 'Gary Fox',
        email: 'gary@fox.com',
        status: 'Inactive',
        role: 'Developer',
      },
      {
        id: 6,
        name: 'Emily Davis',
        email: 'emily@davis.com',
        status: 'Active',
        role: 'Developer',
      },
      {
        id: 9,
        name: 'David Lee',
        email: 'david@lee.com',
        status: 'Inactive',
        role: 'Developer',
      },
    ],
  },
  {
    group: 'Manager',
    collapsed: false,
    rows: [
      {
        id: 3,
        name: 'John Doe',
        email: 'john@doe.com',
        status: 'Active',
        role: 'Manager',
      },
      {
        id: 8,
        name: 'Sarah Wilson',
        email: 'sarah@wilson.com',
        status: 'Active',
        role: 'Manager',
      },
    ],
  },
  {
    group: 'Designer',
    collapsed: false,
    rows: [
      {
        id: 4,
        name: 'Alice Smith',
        email: 'alice@smith.com',
        status: 'Active',
        role: 'Designer',
      },
      {
        id: 10,
        name: 'Olivia Taylor',
        email: 'olivia@taylor.com',
        status: 'Active',
        role: 'Designer',
      },
    ],
  },
  {
    group: 'HR',
    collapsed: false,
    rows: [
      {
        id: 1,
        name: 'Jane Mary',
        email: 'jane@doe.com',
        status: 'Inactive',
        role: 'HR',
      },
      {
        id: 7,
        name: 'Michael Brown',
        email: 'michael@brown.com',
        status: 'Inactive',
        role: 'HR',
      },
      {
        id: 12,
        name: 'Sophia Martinez',
        email: 'sophia@martinez.com',
        status: 'Active',
        role: 'HR',
      },
    ],
  },
  {
    group: 'Tester',
    collapsed: false,
    rows: [
      {
        id: 5,
        name: 'Bob Johnson',
        email: 'bob@johnson.com',
        status: 'Inactive',
        role: 'Tester',
      },
      {
        id: 11,
        name: 'James Anderson',
        email: 'james@anderson.com',
        status: 'Inactive',
        role: 'Tester',
      },
    ],
  },
])
</script>

<template>
  <ListView
    class="h-[250px]"
    :columns="group_columns"
    :rows="grouped_rows"
    :options="{
      getRowRoute: (row) => `/users/${row.id}`,
      selectable: true,
      showTooltip: true,
      resizeColumn: true,
    }"
    row-key="id"
  >
    <template #group-header="{ group }">
      <span class="text-base-medium leading-6 text-ink-gray-9">
        {{ group.group }} ({{ group.rows.length }})
      </span>
    </template>
  </ListView>
</template>
```

## Cell Slot
```vue
<script setup>
import { h, reactive } from 'vue'
import { Avatar, Badge } from 'frappe-ui'
import { ListView } from '..'

const cols = reactive([
  {
    label: 'Name',
    key: 'name',
    width: 3,
    getLabel: ({ row }) => row.name,
    prefix: ({ row }) =>
      h(Avatar, { shape: 'circle', image: row.user_image, size: 'sm' }),
  },
  { label: 'Email', key: 'email', width: '200px' },
  { label: 'Role', key: 'role' },
  { label: 'Status', key: 'status' },
])

const rows = [
  {
    id: 1,
    name: 'John Doe',
    email: 'john@doe.com',
    status: 'Active',
    role: 'Developer',
    user_image: 'https://avatars.githubusercontent.com/u/499550',
  },
  {
    id: 2,
    name: 'Jane Doe',
    email: 'jane@doe.com',
    status: 'Inactive',
    role: 'HR',
    user_image: 'https://avatars.githubusercontent.com/u/499120',
  },
]
</script>

<template>
  <ListView
    class="h-[250px]"
    :columns="cols"
    :rows="rows"
    :options="{
      selectable: true,
      showTooltip: true,
      resizeColumn: true,
      emptyState: {
        title: 'No records found',
        description: 'Create a new record to get started',
        button: {
          label: 'New Record',
          variant: 'solid',
          onClick: () => console.log('New Record'),
        },
      },
    }"
    row-key="id"
  >
    <template #cell="{ item, row, column }">
      <Badge v-if="column.key === 'status'">{{ item }}</Badge>
      <span class="font-medium text-ink-gray-7" v-else>{{ item }}</span>
    </template>
  </ListView>
</template>
```

## Empty List
```vue
<script setup>
import { h, reactive } from 'vue'
import { Avatar } from 'frappe-ui'
import { ListView } from '..'

const cols = reactive([
  {
    label: 'Name',
    key: 'name',
    width: 3,
    getLabel: ({ row }) => row.name,
    prefix: ({ row }) =>
      h(Avatar, { shape: 'circle', image: row.user_image, size: 'sm' }),
  },
  { label: 'Email', key: 'email', width: '200px' },
  { label: 'Role', key: 'role' },
  { label: 'Status', key: 'status' },
])
</script>

<template>
  <ListView
    class="h-[250px]"
    :columns="cols"
    :rows="[]"
    :options="{
      selectable: true,
      showTooltip: true,
      resizeColumn: true,
      emptyState: {
        title: 'No records found',
        description: 'Create a new record to get started',
        button: {
          label: 'New Record',
          variant: 'solid',
          onClick: () => console.log('New Record'),
        },
      },
    }"
  />
</template>
```

## Disabled Rows
```vue
<script setup>
import { h, reactive } from 'vue'

import { Avatar } from 'frappe-ui'
import { ListView } from '..'

const columns = reactive([
  {
    label: 'Name',
    key: 'name',
    width: 3,
    getLabel: ({ row }) => row.name,
    prefix: ({ row }) =>
      h(Avatar, { shape: 'circle', image: row.user_image, size: 'sm' }),
  },
  { label: 'Email', key: 'email', width: '200px' },
  { label: 'Role', key: 'role' },
  { label: 'Status', key: 'status' },
])

const rows = [
  {
    id: 1,
    disabled: true,
    name: 'John Doe',
    email: 'john@doe.com',
    status: 'Active',
    role: 'Developer',
    user_image: 'https://avatars.githubusercontent.com/u/499550',
  },
  {
    id: 2,
    name: 'Jane Doe',
    email: 'jane@doe.com',
    status: 'Inactive',
    role: 'HR',
    user_image: 'https://avatars.githubusercontent.com/u/499120',
  },
]
</script>

<template>
  <ListView
    class="h-[150px]"
    :columns="columns"
    :rows="rows"
    :options="{
      getRowRoute: (row) => `/users/${row.id}`,
      selectable: true,
      showTooltip: true,
      resizeColumn: true,
    }"
    row-key="id"
  />
</template>
```
