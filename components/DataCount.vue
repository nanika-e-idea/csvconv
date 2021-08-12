<template>
  <div class="data_counter">
    <p>ファイル名： {{ fileinfo.filename }}  |  データ件数： {{ fileinfo.datalength }}件  |  重複削除後： {{ fileinfo.corectedlength }}件</p>
  </div>
</template>
<script>
export default({
  props: ['fileinfo'],
})
</script>

<style lang="scss">
  .data_counter{
      width: 100%;
      text-align: center;
  }
</style>