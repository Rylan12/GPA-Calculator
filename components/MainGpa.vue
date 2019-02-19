<template>
  <box>
    <h1>{{value}}</h1>
    <p>{{description}}</p>
  </box>
</template>

<script>
import Box from './Box.vue'

export default {
  name: "MainGpa",
  components: {
    Box,
  },
  props: {
    value: String,
    description: String,
  },
}
</script>

<style scoped>

</style>
