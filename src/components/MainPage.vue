<template>
  <div class="main-page">
    <div class="left-menu">
      左メニュー
    </div>
    <div class="right-view">
      右ビュー
    </div>
  </div>
</template>

<script>
export default {
}
</script>

<style scoped lang="scss">
.main-page {
    display: flex;
    height: calc(100vh - 56px);
    .left-menu {
        width: 300px;
        background-color: #f5f5f5;
    }
    .right-view {
        flex-grow: 1;
        padding: 10px;
    }
}
</style>