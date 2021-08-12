<template>
  <div class="main">
    <h1>CSV preconverter</h1>
    <FileUp @file-object="fileObject($event, files)" />
    <DataCount/>
    <div class="file_dl">
      <button type="button" class="btn btn_dl" @click="fileDL">Step2.整形済みCSVファイルをダウンロードする</button>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';

export default Vue.extend({
  data(){
    return{
      files: [],
      databody: '',
      fileinfo: {
        filename: 'hoge' as String,
        datalength: 0 as Number,
        corectedlength: 0 as Number,
      },
      newdata: '',
      filestatus: 'Empty',
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
      const _this = this;

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
        _this.databody = unicodeArray;
        //console.log(this.databody);

        //ここからセル内改行対応
        var separator = '\t';
        var columnSize = _this.databody.split(/\r\n|\r|\n/)[0].split(separator).length;
        //一文字ずつに分解
        var chars = Array.from(_this.databody.replace(/\r\n|\r|\n/g, '\n'));
        var csvList = [];
        var list = [];
        var queteOpenFlg = false;
        var buf = "";
        for (var i = 0; i < chars.length; i++){
          if(chars[i] == '"') {
            queteOpenFlg = queteOpenFlg == false;　//booleanのトグル処理
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
          } //end if
        } //end for
        console.log(csvList[0][0]);
        var csv: any = {};
        csv.dataList = [];
        for (var i = 0; i < csvList.length; i++) {
          if (i == 0) {
            csv.header = csvList[i];
          } else {
            csv.dataList.push(csvList[i]);
          }
        }

        var ckcolumn = 0;
        var strcsv: string = '';
        var step = 0;
        var str = '';
        var line : any = csvList[0][0].split(',');
        for ( var i = 0; i < line.length; i++) {
          if (line[i] == '適用終了日'){
            ckcolumn = i;
          }
          str += line[i];
          if (i < line.length - 1){
            str += ',';
          } else {
            str += '\n';
          }
        }
        strcsv += str;
        for (var i = 1; i < csvList.length; i++) {
          str = '';
          line = csvList[i][0].split(',');
          //console.log(line[ckcolumn]);
          if (line[ckcolumn] == ''){
            for (var j = 0; j < line.length; j++) {
              var ckrow: any = line[j].split('\n');
              //console.log(ckrow.length); 
              if(ckrow.length > 1){
                str += '"' + line[j] + '"';
              }else{
                str += line[j];
              }
              if (j < line.length - 1){
                str += ',';
              } else {
                str += '\n';
              }
            }
            step++;
            //console.log('step' + step);
          }
          //console.log(str);
          strcsv += str;
        }
        console.log(strcsv);
        //ここまでセル内改行対応

        /**test code
        var test = 'abc,てすと,テ,ス,ト\ntest,"テスト",123,\n';
        **/

        var str2array = function(str:string) {
            var array = [],i,il=str.length;
            for(i=0;i<il;i++) array.push(str.charCodeAt(i));
            return array;
        };

        var returnArray = encoding.convert(str2array(strcsv), 'SJIS', 'UNICODE');
        var returnstr: any = new Uint8Array( returnArray );
        //ファイル名、件数取得
        _this.fileinfo = {
          filename : file.name,
          datalength: csvList.length - 1,
          corectedlength: step,
        };
        _this.newdata = returnstr;
        //this.fileinfo.filename = file.name;
        //this.fileinfo.datalength = rows.length;
        console.log(_this.fileinfo);
        //console.log(this.databody);
        _this.filestatus = 'Standby';
      };
        //this.filestatus = 'Error';
    },
    fileDL(){
      let blob = new Blob([this.newdata],{type:"text/plan"});
      let link = document.createElement('a');
      link.href = URL.createObjectURL(blob);
      link.download = 'corrected_' + this.fileinfo.filename;
      link.click();
      //console.log(datastream);
      //console.log(this.fileinfo);
    },
  },
})
</script>

<style>
/**fileDL**/
  .file_dl{
      width: 100%;
      text-align: center;
  }
  .btn_dl{
      box-sizing: border-box;
      width: 900px;
      height: 50px;
      max-width: 90%;
      border: none;
      border-radius: 25px;
      background-color: #29a;
      font-size: 20px;
      font-weight: 600;
      color:white;
      filter:drop-shadow(2px 2px 1px rgba(0,0,0,0.5)); 
  }
</style>