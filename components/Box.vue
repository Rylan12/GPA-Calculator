<template>
  <div class="box">
    <slot/>
  </div>
</template>

<script>
export default {
  name: "Box",
}
</script>

<style scoped>
.box {
  padding: 10px;
  width: 155px;
  text-align: center;
  float: left;
}
.box:not(:last-child) {
  border-right: 1px solid #d0d3d9;
}
.box * {
  color: #35485d;
  font-weight: 300;
}
.box p {
  font-weight: 400;
}
</style>
