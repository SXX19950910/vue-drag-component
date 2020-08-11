<template>
    <div id="usually-warp">
        <div class="left-box">
            <div class="fun-list">
                <div class="head-area">
                    <div class="item">功能名称</div>
                    <div class="item">描述</div>
                    <div class="item">操作</div>
                </div>
                <div class="body-area">
                    <div class="column-item">
                        <div class="left-name">
                            Parent
                        </div>
                        <div class="middle-desc">
                            <span class="describe">是否以父元素为拖拽参照元素，否则为document </span>
                        </div>
                        <div class="right-handle">
                            <label>
                                <input v-model="drag.parent" type="checkbox" />
                            </label>
                        </div>
                    </div>
                    <div class="column-item">
                        <div class="left-name">
                            Resizable
                        </div>
                        <div class="middle-desc">
                            <span class="describe">是否启用缩放</span>
                        </div>
                        <div class="right-handle">
                            <label>
                                <input v-model="drag.resizable" type="checkbox" />
                            </label>
                        </div>
                    </div>
                    <div class="column-item">
                        <div class="left-name">
                            ResizeInstance
                        </div>
                        <div class="middle-desc">
                            <span class="describe">是否将四周的缩放控件的宽高放入计算公式</span>
                        </div>
                        <div class="right-handle">
                            <label>
                                <input v-model="drag.resizeInstance" type="checkbox" />
                            </label>
                        </div>
                    </div>
                    <div class="column-item">
                        <div class="left-name">
                            draggable
                        </div>
                        <div class="middle-desc">
                            <span class="describe">是否允许拖拽</span>
                        </div>
                        <div class="right-handle">
                            <label>
                                <input v-model="drag.draggable" type="checkbox" />
                            </label>
                        </div>
                    </div>
                    <div class="column-item">
                        <div class="left-name">
                            disableUserSelect
                        </div>
                        <div class="middle-desc">
                            <span class="describe">是否允许用户拖拽选中内容</span>
                        </div>
                        <div class="right-handle">
                            <label>
                                <input v-model="drag.disableUserSelect" type="checkbox" />
                            </label>
                        </div>
                    </div>
                    <div class="column-item">
                        <div class="left-name">
                            helpLine
                        </div>
                        <div class="middle-desc">
                            <span class="describe">是否启用辅助线</span>
                        </div>
                        <div class="right-handle">
                            <label>
                                <input v-model="drag.helpLine" type="checkbox" />
                            </label>
                        </div>
                    </div>
                    <div class="column-item">
                        <div class="left-name">
                            axis
                        </div>
                        <div class="middle-desc">
                            <span class="describe">可拖拽的方向 参数为 x, y , xy 默认 xy</span>
                        </div>
                        <div class="right-handle">
                            <label>
                                <select v-model="drag.axis">
                                    <option value="xy">xy</option>
                                    <option value="y">y</option>
                                    <option value="x">x</option>
                                </select>
                            </label>
                        </div>
                    </div>
                    <div class="column-item">
                        <div class="left-name">
                            grid
                        </div>
                        <div class="middle-desc">
                            <span class="describe">网格拖拽 默认 [0, 0]</span>
                        </div>
                        <div class="right-handle">
                            <label>
                                x:<input v-model="drag.grid[0]" class="ipt" type="number" />
                                y:<input v-model="drag.grid[1]" class="ipt" type="number" />
                            </label>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="right-box">
            <vue-drag-component v-bind="drag" :active.sync="active">
                <div class="box">
                    contentcontentcontentcontentcontentcontentcontentcontentcontent
                </div>
            </vue-drag-component>
        </div>
    </div>
</template>

<script>
    import vueDragComponent from './../../components/drag.vue'
    export default {
        components: {
          vueDragComponent
        },
        data() {
            return {
                active: false,
                drag: {
                    parent: true,
                    resizable: true,
                    resizeInstance: false,
                    draggable: true,
                    disableUserSelect: true,
                    helpLine: false,
                    axis: 'xy',
                    grid: [0, 0]
                }
            }
        },
        methods: {
            onParentChange() {
                console.log(this.drag)
            }
        }
    }
</script>

<style lang="scss">
    #usually-warp {
        display: flex;
        height: 100%;
        .left-box {
            .fun-list {
                .ipt {
                    width: 60px;
                }
                .head-area {
                    width: 100%;
                    display: flex;
                    justify-content: space-between;
                    min-height: 40px;
                    background-color: white;
                    align-items: center;
                    padding: 0 15px;
                    font-size: 14px;
                    border-bottom: 1px solid #dedede;
                }
                .body-area {
                    .column-item {
                        display: flex;
                        align-items: center;
                        justify-content: space-between;
                        padding: 0 15px;
                        min-height: 40px;
                        background-color: white;
                        color: #666;
                        border-bottom: 1px solid #dedede;
                        font-size: 14px;
                        .describe {
                            font-size: 14px;
                            margin-left: 5px;
                            color: #999999;
                        }
                        &:last-child {
                            border-bottom: 0;
                        }
                        .left-name {
                            width: 30%;
                        }
                        .middle-desc {
                            width: 50%;
                        }
                        .right-handle {
                            width: 20%;
                            text-align: right;
                        }
                    }
                }
            }
        }
        .right-box {
            width: 50%;
            .box {
                width: 100%;
                min-width: 200px;
                min-height: 200px;
                height: 100%;
                border: 1px dashed #333;
                word-break: break-word;
            }
        }
    }
</style>