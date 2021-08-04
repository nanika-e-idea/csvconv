<template>
  <div class="main">
    <h1>CSV preconverter</h1>
    <FileUp @file-object="fileObject($event, files)" />
    <DataCount/>
    <FileDL/>
    <span>{{ this.fileinfo.filename }}</span>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';

export default Vue.extend({
  data(){
    return{
      files: [],
      filename: null,
      databody: null,
      fileinfo: {
        'filenmae': null,
        'datalength': null,
        'corectedlength': null
      },
      buff: null,
      sjisArray: null,
      unicodeArray: null,
    }
  },
  methods: {
    fileObject(files: []){
      this.files = files;
      //console.log('[index]file:' + this.files[0].name);
      let file = this.files[0];
      
      const encoding = require('encoding-japanese');
      const reader = new FileReader();
      
      reader.readAsArrayBuffer(file);
      //this.buff = reader.result;
      //reader.readAsText(file);
      reader.onload = () => {
        //let str = reader.result;
        //console.log(str);
        this.buff = reader.result;
        this.sjisArray = new Uint8Array(this.buff);
        this.unicodeArray = encoding.convert(this.sjisArray, {
          to: 'UNICODE',
          from: 'SJIS',
          type: 'string'
        });
        this.databody = this.unicodeArray;
        console.log(this.databody);
      }
      

      //sjis>>unicode
      //var str = reader.result;
      

      //行ごとに分解
      
      //ファイル名、件数取得
      //this.fileinfo.filenmae = file.name;
      //console.log(this.fileinfo);
      //console.log(this.databody);
      
      //var rows = files[0].data.split(/\r\n|\n/);
      //for(var i = 0; i < rows.length; i++){
        //
      //}
      
      //this.fileinfo.datalength = rows.length -1;
      
    },
  },
})
</script>
