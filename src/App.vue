<template>
  <div>
    <app-header v-if="this.$route.meta.header !== false" />
    <router-view />
  </div>
</template>

<script>
import Header from './components/layout/Header'

export default {
  components: {
    'app-header': Header
  }
}
</script>

<style src="./assets/style/main.css">
body {
  font-family: 'S-CoreDream-3Light' !important;
}
</style>
