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
        var chars = Array.form(this.databody.replace(/\r\n|\r|\n/g, '\n'));
        var csvList = [];
        var list = [];
        var queteOpenFlg = false;
        var buf = "";
        for (var i = 0; i < chars.length; i++){
          console.log(columnSize);
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
        console.log(csvList[0][0]);
        var csv = {};
        csv.dataList = [];
        for (var i = 0; i < csvList.length; i++) {
          if(i == 0) {
            csv.header = csvList[i];
          } else {
            csv.dataList.push(csvList[i]);
          }
        }
        //ここまでセル内改行対応
        //var rows = this.databody.split(/\r\n|\r|\n/);
        //判定用カラム検索
        var headarr = rows[0].split(',');
        var ckcolumn = 0;
        for (var i = 0; i < headarr.length; i++){
          if(headarr[i] == '適用終了日'){
            ckcolumn = i;
            break;
          }
        }
        console.log('ckcolumn: ' + ckcolumn);
        //重複データ削除
        var rerows = [];
        rerows[0] = rows[0];
        var step = 1;
        for (var i = 1; i < rows.length; i++){
          console.log(rows[i]);
          var arr = rows[i].split(',');
          if (arr[ckcolumn] = ''){
            rerows[step] = rows[i];
            step++;
          }
        }
        //console.log('datalegth: ' + parseInt(rows.length - 1) + ' >> ' + parseInt(rerows.length - 1));
        //ファイル名、件数取得
        //console.log('fileinfo: ' + this.fileinfo);
        this.fileinfo = {
          filename : file.name,
          datalength: rows.length - 1,
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
