<template>
  <p class="version-text">Version {{ version }}</p>
</template>

<script>
  export default {
    name: "VersionText",
    props: ['version']
  }
</script>

<style scoped>
  .version-text {
    font-size: x-small;
  }
</style>
