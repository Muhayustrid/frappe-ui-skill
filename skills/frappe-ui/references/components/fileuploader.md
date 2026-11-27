<!-- Upstream: https://github.com/frappe/frappe-ui/blob/b435c66b51c0b19425a2a049b43e3ef2e308a8ab/src/components/FileUploader/FileUploader.md -->

# FileUploader

Opens the file picker, uploads the chosen file to a Frappe site and reports
the progress through its slot. For drag and drop, several files at once, or an
upload with no trigger, use
[`useFileUpload()`](../other/utilities.md#usefileupload-fileuploadhandler)
instead.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import {
  Button,
  ErrorMessage,
  FileUploader,
  type UploadedFile,
} from 'frappe-ui'

const attachments = ref([
  {
    file_name: 'contract-draft.pdf',
    file_url: '/private/files/contract-draft.pdf',
  },
  { file_name: 'site-photos.zip', file_url: '/private/files/site-photos.zip' },
])

function onSuccess(file: UploadedFile) {
  attachments.value.push(file)
}
</script>

<template>
  <div class="w-80 space-y-2">
    <div class="text-base-medium text-ink-gray-8">Attachments</div>
    <div
      class="divide-y divide-outline-gray-1 rounded-6 border border-outline-gray-1"
    >
      <div
        v-for="attachment in attachments"
        :key="attachment.file_url"
        class="flex items-center gap-2 px-3 py-2 text-base text-ink-gray-7"
      >
        <span
          class="lucide-paperclip size-4 text-ink-gray-5"
          aria-hidden="true"
        />
        <span class="truncate">{{ attachment.file_name }}</span>
      </div>
    </div>
    <FileUploader
      doctype="Project"
      docname="PROJ-0042"
      :file-types="['.pdf', '.docx', '.zip', 'image/*']"
      @success="onSuccess"
    >
      <template
        #default="{ file, uploading, progress, error, openFileSelector }"
      >
        <div v-if="uploading" class="space-y-1 px-1">
          <div class="flex justify-between text-sm text-ink-gray-6">
            <span class="truncate">{{ file?.name }}</span>
            <span>{{ progress }}%</span>
          </div>
          <div class="h-1 rounded-full bg-surface-gray-2">
            <div
              class="h-1 rounded-full bg-surface-gray-7"
              :style="{ width: `${progress}%` }"
            />
          </div>
        </div>
        <Button
          v-else
          variant="ghost"
          icon-left="lucide-plus"
          label="Attach file"
          @click="openFileSelector"
        />
        <ErrorMessage v-if="error" :message="error" class="mt-1 px-1" />
      </template>
    </FileUploader>
  </div>
</template>
```

## Examples

### Profile photo

The default slot draws the trigger. `openFileSelector` opens the picker, and
`uploading`, `progress` and `error` drive the button and the message below it.
The photo is public (`:private="false"`) so it can show where no one is
signed in, and `validateFile` rejects images over 2 MB before they upload.
The docs site has no Frappe server, so uploads on this page end with an error.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import {
  Avatar,
  Button,
  ErrorMessage,
  FileUploader,
  type UploadedFile,
} from 'frappe-ui'

const photo = ref('https://i.pravatar.cc/80?u=jane@example.com')

function checkSize(file: File) {
  if (file.size > 2 * 1024 * 1024) return 'Pick an image under 2 MB.'
}

function onSuccess(file: UploadedFile) {
  photo.value = file.file_url
}
</script>

<template>
  <FileUploader
    file-types="image/*"
    :private="false"
    doctype="User"
    docname="jane@example.com"
    fieldname="user_image"
    optimize
    :validate-file="checkSize"
    @success="onSuccess"
  >
    <template #default="{ uploading, progress, error, openFileSelector }">
      <div class="flex items-center gap-4">
        <Avatar :image="photo" label="Jane Cooper" size="3xl" />
        <div class="space-y-1.5">
          <Button
            :loading="uploading"
            :loading-text="`Uploading ${progress}%`"
            label="Change photo"
            @click="openFileSelector"
          />
          <ErrorMessage v-if="error" :message="error" />
          <p v-else class="text-sm text-ink-gray-5">Any image up to 2 MB</p>
        </div>
      </div>
    </template>
  </FileUploader>
</template>
```

## Behavior

### Private by default

Uploads are private (`is_private=1`) unless you pass `:private="false"`. A
private file needs a signed-in session to download. Make a file public only
when it must load without one, such as a profile photo in an email digest.

### Where the file goes

- `folder` sets the folder in Frappe's file manager. It defaults to `Home`.
- `doctype`, `docname` and `fieldname` attach the file to a document and a
  field on it.
- `uploadEndpoint` sets the URL the file is posted to. It defaults to
  `/api/method/upload_file`.
- `optimize` asks the server to resize an image before it stores it.

### Picking a file

`fileTypes` sets the file types the picker accepts, as a string or an array,
such as `'image/*'` or `['.pdf', '.docx']`. The picker takes one file at a
time.

### Validation

`validateFile` runs on the chosen file before the upload starts. Return a
message or an `Error` to stop the upload, or nothing to let it through. It can
be async, and an error it throws also stops the upload. The message shows as
`error` in the slot, and `failure` fires with it.

### Slot props

Without a default slot, FileUploader renders an "Upload File" button that
shows the progress, and an error message under it. The default slot replaces
both and receives:

| Prop               | Value                                              |
| ------------------ | -------------------------------------------------- |
| `openFileSelector` | Opens the file picker                              |
| `file`             | The chosen `File`, or `null`                       |
| `uploading`        | `true` while the file uploads                      |
| `progress`         | Percent uploaded, from `0` to `100`                |
| `uploaded`         | Bytes uploaded so far                              |
| `total`            | Total bytes to upload                              |
| `success`          | `true` once the upload finished without an error   |
| `error`            | The error message as a string, or `null`           |
| `message`          | Not set by the component; always an empty string   |

### Events

`success` fires with the uploaded file record, which has `file_name`,
`file_url` and `file_size`. `failure` fires when validation stops the file or
the request fails.

## API reference

```ts
const propsData = [
  {
    name: 'fileTypes',
    description: 'Accepted file types passed to the native file input.',
    required: false,
    type: 'string | string[]'
  },
  {
    name: 'private',
    description: 'Whether the uploaded file is access-controlled. Defaults to `true` — an\nupload with no stated intent is private, not world-readable. Pass\n`false` only for intentionally public files.',
    required: false,
    type: 'boolean',
    default: 'true'
  },
  {
    name: 'folder',
    description: 'Folder to upload into, on Frappe\'s file manager. Defaults to `Home`.',
    required: false,
    type: 'string'
  },
  {
    name: 'doctype',
    description: 'Doctype to attach the uploaded file to.',
    required: false,
    type: 'string'
  },
  {
    name: 'docname',
    description: 'Name of the document to attach the uploaded file to.',
    required: false,
    type: 'string'
  },
  {
    name: 'fieldname',
    description: 'Fieldname on the document the uploaded file belongs to.',
    required: false,
    type: 'string'
  },
  {
    name: 'uploadEndpoint',
    description: 'Endpoint the file is posted to. Defaults to `/api/method/upload_file`.',
    required: false,
    type: 'string'
  },
  {
    name: 'optimize',
    description: 'Resize the image server-side before storing it.',
    required: false,
    type: 'boolean'
  },
  {
    name: 'validateFile',
    description: 'Optional validation hook. Return a message or Error to block upload.',
    required: false,
    type: '((file: File) => FileUploaderValidationResult | Promise<FileUploaderValidationResult>)'
  }
]

  const slotsData = [
  {
    name: 'default',
    description: '',
    type: 'FileUploaderSlotProps'
  }
]

  const emitsData = [
  {
    name: 'success',
    description: 'The file finished uploading.',
    type: '[data: UploadedFile]'
  },
  {
    name: 'failure',
    description: 'The upload failed — validation or the request itself.',
    type: '[error: unknown]'
  }
]
```
