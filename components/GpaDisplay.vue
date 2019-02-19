<template>
  <div class="grade-display">
    <main-gpa :value="change(weighted)" description="Weighted GPA"/>
    <main-gpa :value="change(unweighted)" description="Unweighted GPA"/>
  </div>
</template>

<script>
import MainGpa from './MainGpa.vue'

export default {
  name: "GpaDisplay",
  components: {
    MainGpa,
  },
  props: {
    weighted: Number,
    unweighted: Number,
  },
  methods: {
    change(gpa) {
      if (gpa === null) {
        return '-';
      }
      return '' + gpa;
    }
  },
}
</script>

<style scoped>
.grade-display {
  width: 100%;
  background-color: white;
  position: fixed;
  bottom: 0;
  border-top: solid #ebeef5 1px;
  z-index: 99;
  padding: 10px;
}
</style>
