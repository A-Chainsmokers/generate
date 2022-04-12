<template>
  <div id="app" class="container">
    <index/>
  </div>
</template>

<script>
import index from '@/components/index'
export default {
  name: 'App',
  components: {
    index
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
