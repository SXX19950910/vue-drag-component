<template>
  <div id="app">
    <div class="board-warp">
      <vue-drag-component v-for="item in list" :key="item.id" :active.sync="item.active" :z="item.zIndex" :help-line="true" line-class="new-class-line" :init-height="100" :init-width="100" :grid="[100, 100]" drag-filter="test-1" :component-data="item" :left="400" :top="400" @active="onActive" @resize-stop="onResizeStop" @drag-stop="onDragStop" @dragging="onDragMove" @resizing="onResize">
        <div class="demo">
          点我可以拖拽
          <div class="test-1">点我无法拖拽</div>
          <div class="test-4">点我可以拖拽</div>
          <div class="test-2">点我可以拖拽</div>
        </div>
      </vue-drag-component>
    </div>
  </div>
</template>

<script>
  import { on } from '@/utils/dom.js'
  import vueDragComponent from './components/drag'
  export default {
    name: 'App',
    components: {
      vueDragComponent
    },
    data() {
      return {
        list: [
          {
            id: 0,
            active: false,
            zIndex: 1
          }
        ]
      }
    },
    mounted() {
      const that = this
      on(document, 'mousedown', function () {
        that.list.map(item => {
          item.active = false
        })
      })
    },
    methods: {
      onDragMove() {
        // console.log(e)
      },
      onResizeStop() {
        // console.log(local)
      },
      onDragStop() {
        // console.log(position)
      },
      onResize() {
        // console.log(e, s)
      },
      onActive() {
        // console.log(e, data)
      }
    }
  }
</script>

<style lang="scss">
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  #app {
    height: 100vh;
    background-color: black;
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    box-sizing: border-box;
    overflow: hidden;
    .board-warp {
      width: 100%;
      height: 100%;
      background: linear-gradient(-90deg, rgba(0, 0, 0, 0.1) 1px, transparent 1px) 0% 0% / 100px 100px, linear-gradient(rgba(0, 0, 0, 0.1) 1px, transparent 1px) 0% 0% / 100px 100px;
      background-color: white;
    }
    .vue-drag-warp {
      .demo {
        white-space: normal;
        word-break: break-word;
        height: 100%;
        border: 1px dashed #333333;
      }
      &.dragging {
        background-color: #DC4E41;
        color: white;
      }
      &.resizing {
        background-color: #FFBE00;
        color: white;
      }
    }
  }
</style>
