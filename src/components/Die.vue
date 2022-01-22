<template>
  <div class="die">{{value}}</div>
</template>
<script>
export default {
  props:{
    value: Number
  },
}
</script>
<style scoped>
  .die {
    padding: 1rem;
    background-color: lightgrey;
    border-radius: 4px;
    font-weight: bold;
  }
</style>

