<template>
  <div class="container">
    <el-container>
      <el-main>
        <h1 class="title">
          GPA Calculator
        </h1>
        <h2 class="subtitle">
          By Rylan Polster
        </h2>
        <grade-table @change="$set(gpa, 0, $event)"/>
      </el-main>
    </el-container>
    <gpa-display :weighted="gpa[0][0]" :unweighted="gpa[0][1]"/>
  </div>
</template>

<script>
import GradeTable from '~/components/GradeTable.vue'
import GpaDisplay from '~/components/GpaDisplay.vue'

export default {
  components: {
    GradeTable,
    GpaDisplay,
  },
  data() {
    return {
      gpa: [[null, null]],
    }
  },
}
</script>

<style lang="scss">
@import "../assets/main";

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
  'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.container {
  margin-bottom: $footer-height;
}

main {
  padding-bottom: 0 !important;
}
</style>
