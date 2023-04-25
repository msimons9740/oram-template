<template>
  <button
    type="button"
    :class="`btn btn-${type}`">{{ caption }}</button>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  props: {
    caption: {
      type: String,
      default: 'Button',
    },
    type: {
      type: String,
      default: 'secondary',
    },
  },
})
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
</style>
