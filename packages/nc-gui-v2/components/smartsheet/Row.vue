<script lang="ts" setup>
import type { Row } from '~/composables'
import { useProvideSmartsheetRowStore, useSmartsheetStoreOrThrow } from '#imports'

interface Props {
  row: Row
}

const props = defineProps<Props>()
const currentRow = toRef(props, 'row')

const { meta } = useSmartsheetStoreOrThrow()
const { isNew, state, syncLTARRefs } = useProvideSmartsheetRowStore(meta, currentRow)

// on changing isNew(new record insert) status sync LTAR cell values
watch(isNew, async (nextVal, prevVal) => {
  if (prevVal && !nextVal) {
    await syncLTARRefs(currentRow.value.row)
    // update row values without invoking api
    currentRow.value.row = { ...currentRow.value.row, ...state.value }
    currentRow.value.oldRow = { ...currentRow.value.row, ...state.value }
  }
})
</script>

<template>
  <slot :state="state" />
</template>
