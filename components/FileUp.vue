<template>
    <div class="file_up">
        <div class="droparea">
            ここにドラッグ＆ドロップ
        </div>
    </div>
</template>
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
</style>