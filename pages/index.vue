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

        //行ごとに分割
        //ここからセル内改行対応
        var separator = '\t';
        var columnSize = this.databody.split(/\r\n|\r|\n/)[0].split(separator).length;
        var chars = Array.from(this.databody.replace(/\r\n|\r|\n/g, '\n'));
        var csvList = [];
        var list = [];
        var queteOpenFlg = false;
        var buf = "";
        for (var i = 0; i < chars.length; i++){
          //console.log(columnSize);
          if(chars[i] == '"') {
            queteOpenFlg = queteOpenFlg == false;
          } else {
            if((chars[i] == separator || chars[i] == '\n') && !queteOpenFlg) {
              list.push(buf);
              buf = "";
              if (list.length == columnSize) {
                csvList.push(list);
                list = [];
              }
            } else {
              buf += chars[i];
            }
          }
        }
        //console.log(csvList[0][0]);
        var csv: any = {};
        csv.dataList = [];
        for (var i = 0; i < csvList.length; i++) {
          if(i == 0) {
            csv.header = csvList[i];
          } else {
            csv.dataList.push(csvList[i]);
          }
        }
        //ここまでセル内改行対応

        //判定用カラム検索
        var headarr = csvList[0][0].split(',');
        var ckcolumn = 0;
        for (var i = 0; i < headarr.length; i++){
          //console.log(headarr[i]);
          if(headarr[i] == '適用終了日'){
            ckcolumn = i;
            break;
          }
        }
        //console.log('ckcolumn: ' + ckcolumn);

        //重複データ削除
        var newrows = [];
        newrows[0] = csvList[0][0];
        var step = 1;
        for (var i = 1; i < csvList.length; i++){
          var arr = csvList[i][0].split(',');
          //console.log(arr[ckcolumn]);
          if (!arr[ckcolumn]){
            newrows[step] = csvList[i][0];
            step++;
          } //end if
        } //end for
        
        //ファイル名、件数取得
        this.fileinfo = {
          filename : file.name,
          datalength: csvList.length - 1,
          corectedlength: newrows.length -1,
        };
        this.newdata = newrows;
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
