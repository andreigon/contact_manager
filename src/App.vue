<script>
import Header from '@/components/Header.vue'

export default {
  components: {
    Header
  }
}
</script>

<template>
  <div class="wrapper">
    <Header />
    <div class="wrapper-content">
      <router-view />
    </div>
  </div>
</template>

