<template>
  <div class="spinner-wrapper">
    <div
      :style="{ width: `${width}px`, height: `${height}px` }"
      class="spinner"
    />
  </div>
</template>

<script>
export default {
  props: {
    width: {
      type: Number,
      default: 40
    },
    height: {
      type: Number,
      default: 40
    }
  }
}
</script>
