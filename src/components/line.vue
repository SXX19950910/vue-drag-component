<template>
    <div class="vue-line-warp">
        <div class="x-line" />
        <div class="y-line" />
    </div>
</template>

<script>
    export default {
        data() {
            return {
            }
        }
    }
</script>

<style lang="scss">
    .vue-line-warp {
        display: flex;
        justify-content: center;
        align-items: center;
        width: 100%;
        height: 100%;
        position: absolute;
        top: 0;
        left: 0;
        z-index: -1;
        .x-line {
            width: 100vw;
            position: absolute;
            height: 1px;
            background-color: rgba(25,143,255, 0.6);
        }
        .y-line {
            height: 100vh;
            position: absolute;
            width: 1px;
            background-color: rgba(25,143,255, 0.6);
        }
    }
</style>