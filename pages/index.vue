<template>
  <div class="main">
    <h1>CSV preconverter</h1>
    <FileUp @file-object="fileObject($event, files)" />
    <DataCount/>
    <FileDL/>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';

export default Vue.extend({
  data(){
    return{
      files: [],
      //filename: '',
      databody: '',
      fileinfo: {
        filename: 'hoge' as String,
        datalength: 0 as Number,
        corectedlength: 0 as Number,
      },
      newdata: '',
      //buff: null,
      //sjisArray: null,
      //unicodeArray: null,
    }
  },
  methods: {
    fileObject(files: []){
      this.files = files;
      let file: any = this.files[0];
      //console.log('[index]file:' + this.files[0].name);
      
      const reader: any = new FileReader();
      
      reader.readAsArrayBuffer(file);
      //reader.readAsText(file);
      
      reader.onload = function() {
        const encoding = require('encoding-japanese');
        var sjisArray = new Uint8Array(reader.result);
        var unicodeArray = encoding.convert(sjisArray, {
          to: 'UNICODE',
          from: 'SJIS',
          type: 'string'
        });
        this.databody = unicodeArray;
        //console.log(this.databody);

        //行ごとに分解
        var rows = this.databody.split(/\r\n|\n/);
        //判定用カラム検索
        var headarr = rows[0].slplit(",");
        var ckcolumn = 0;
        for (var i = 0; i < headarr.length; i++){
          if(headarr[i] == '適用終了日'){
            ckcolumn = i;
            break;
          }
        }
        //重複データ削除
        var rerows = [];
        //ファイル名、件数取得
        //console.log('fileinfo: ' + this.fileinfo);
        this.fileinfo = {
          filename : file.name,
          datalength: rows.length,
        };
        //this.fileinfo.filename = file.name;
        //this.fileinfo.datalength = rows.length;
        console.log(this.fileinfo);
        //console.log(this.databody);
      
      //var rows = files[0].data.split(/\r\n|\n/);
      //for(var i = 0; i < rows.length; i++){
      //
      //}
      
      //this.fileinfo.datalength = rows.length -1;

      };

      


      
    },
  },
})
</script>
