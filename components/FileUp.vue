<template>
  <div class="file_up">
    <div class="droparea" :class="{'enter': this.isEnter}" @click="clickEvent" @dragover.prevent="dragOver" @drop.stop="dropFile">
      Step1.CSVファイルをここにドロップ
    </div>
  </div>
</template>

<script>
export default{
  data: {
      isEnter: false,
  },
  methods: {
    clickEvent() {
      if(this.isEnter == true){
        this.isEnter = false;
      }else{
        this.isEnter = true;
      }
      console.log(this.isEnter);
    },
    dragOver() {
      console.log('EVENT[dragOver]');
      this.isEnter = true;
      return;
    },
    dragLeave() {
      console.log('EVENT[dragLeave]');
      this.isEnter = false;
    },
    dropFile(event) {
      console.log('EVENT[dropFile]');
      this.isEnter = false;
      const files = event.dataTransfer.files;
      return　 false;
      //ToDo ファイル種別チェック
      
      //Todo 行ごとに配列に格納

      //１行目、カンマで分割して、重複判定カラム位置取得、出力ファイル先頭行生成

      //２行目以降、判定カラムの値で出力ファイル書き出し判定
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