<template>
    <div class="vue-resize-warp">
        <div v-for="(item, index) in handleList" class="handle" :key="index" :class="item.className" @click.stop @mousedown="handleMouseDown"></div>
    </div>
</template>

<script>
    export default {
        props: {
            handles: {
                type: Array,
                default() {
                    return ['tl', 'tm', 'tr', 'ml', 'mr', 'bl', 'bm', 'br']
                }
            }
        },
        data() {
            return {
            }
        },
        computed: {
          handleList() {
              const list = [
                  {
                      id: 'tl',
                      className: 'handle-tl',
                      event: 'resize-tl'
                  },
                  {
                      id: 'tm',
                      className: 'handle-tm',
                      event: 'resize-tm'
                  },
                  {
                      id: 'tr',
                      className: 'handle-tr',
                      event: 'resize-tr'
                  },
                  {
                      id: 'mr',
                      className: 'handle-mr',
                      event: 'resize-mr'
                  },
                  {
                      id: 'br',
                      className: 'handle-br',
                      event: 'resize-br'
                  },
                  {
                      id: 'bm',
                      className: 'handle-bm',
                      event: 'resize-bm'
                  },
                  {
                      id: 'bl',
                      className: 'handle-bl',
                      event: 'resize-bl'
                  },
                  {
                      id: 'ml',
                      className: 'handle-ml',
                      event: 'resize-ml'
                  }
              ]
              const result = []
              this.handles.map(item => {
                  const current = list.find(tag => item === tag.id )
                  current && result.push(current)
              })
              return result
          }
        },
        methods: {
            handleMouseDown(e) {
                const currentEvent = this.handleList.find(item => item.className === e.target.classList[1]).event;
                this.$emit(currentEvent, e);
            }
        }
    }
</script>

<style lang="scss">
    .vue-resize-warp {
        .handle {
            box-sizing: border-box;
            position: absolute;
            width: 10px;
            height: 10px;
            background: #EEE;
            border: 1px solid #333;
            &.handle-tl {
                top: -10px;
                left: -10px;
                cursor: nw-resize;
            }
            &.handle-tm {
                top: -10px;
                left: 50%;
                margin-left: -5px;
                cursor: n-resize;
            }
            &.handle-tr {
                top: -10px;
                right: -10px;
                cursor: ne-resize;
            }
            &.handle-mr {
                top: 50%;
                margin-top: -5px;
                right: -10px;
                cursor: e-resize;
            }
            &.handle-br {
                bottom: -10px;
                right: -10px;
                cursor: se-resize;
            }
            &.handle-bm {
                bottom: -10px;
                left: 50%;
                margin-left: -5px;
                cursor: s-resize;
            }
            &.handle-bl {
                bottom: -10px;
                left: -10px;
                cursor: sw-resize;
            }
            &.handle-ml {
                top: 50%;
                margin-top: -5px;
                left: -10px;
                cursor: w-resize;
            }
        }
    }
</style>