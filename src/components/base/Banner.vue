<template>
  <div class="banner">
  </div>
</template>

<script>
  export default {
    name: 'Banner'
  }
</script>

<style scoped lang="scss">
  .banner {
    height: 450px;
    background: url("../../assets/images/bg.jpg") no-repeat center center;
    /*background: url("http://qbaj8dxcr.bkt.clouddn.com/bg.jpg") no-repeat center center;*/
    background-size: cover;
  }
  @media (max-width: 768px) {
    .banner {
      max-height: 300px;
    }
  }
</style>
