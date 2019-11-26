<template>
  <div id="app">
    <Tag />
  </div>
</template>

<script>
import Tag from './components/Tag.vue'

export default {
  name: 'app',
  components: {
    Tag
  }
}
</script>

<style lang="scss">
#app {
  @import url('https://fonts.googleapis.com/css?family=PT+Sans:400,700&display=swap&subset=cyrillic');
  font-family: 'PT Sans', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
