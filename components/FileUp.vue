<template>
  <div class="file_up">
    <div class="droparea" 
    :class="{'enter': this.onDrag}" 
    @dragenter="dragEnter" 
    @dragleave="dragLeave" 
    @dragover.prevent 
    @drop.prevent.stop="dropFile">
      Step1.CSVファイルをここにドロップ
    </div>
  </div>
</template>

<script>
export default{
  data(){
    return{
      onDrag: false,
      files: [],
      filename: null,
      datalength: null,
    };
  },
  mounted(){
    //this.$emit('file-object', this.Files);
  },
  methods: {
    clickEvent() {
      return;
    },
    dragEnter() {
      //console.log('EVENT[dragEnter]');
      this.onDrag = true;
    },
    dragLeave() {
      //console.log('EVENT[dragLeave]');
      this.onDrag = false;
    },
    dropFile(e) {
      //console.log('EVENT[drpFile]');
      //console.log( e.dataTransfer.files);
      this.onDrag = false;
      this.files = [ ...e.dataTransfer.files];
      //ファイル名取得
      this.filename = this.files[0].name
      console.log('[FileUp]file:' + this.filename);
      this.$emit('file-object', this.files);
    },
  },
}
</script>

<style type="scss">
  .file_up{
    display: flex;
    justify-content: center;
  }
  .droparea{
    box-sizing: border-box;
    border: dashed 3px #aaa;
    border-radius: 30px;
    width: 900px;
    height: 400px;
    max-width: 90%;
    max-height: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 40px;
    font-weight: 600;
    color: #aaa;
  }
  .enter{
    background-color: #cef;
  }
</style>