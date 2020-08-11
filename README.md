# vue-drag-component
> Vue component for draggable and resize element
#### 安装 Install
    npm i vue-drag-component -S
#### 基本用法 Basic
register
```javascript
   import vueDragComponent from 'vue-drag-component'
   import 'vue-drag-component/dist/vueDragComponent.css'
   Vue.component('vue-drag-component', vueDragComponent)
```
use
```vue
    <vue-drag-component :parent="true" :active.sync="active">
        <div class="box"></div>
    </vue-drag-component>
```
complete
```vue
<template>
    <div id="basic-warp">
        <div class="right-box">
            <vue-drag-component :parent="true" :active.sync="active">
                <div class="box"></div>
            </vue-drag-component>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                active: false
            }
        }
    }
</script>

<style lang="scss">
    #basic-warp {
        height: 100%;
        display: flex;
        .box {
            width: 100%;
            min-width: 200px;
            min-height: 200px;
            height: 100%;
            border: 1px dashed #333;
        }
    }
</style>
```
#### 属性 Props
名称 name  | 类型 type | 描述 describe | 值 value
 ---- | ----- | ------ | ----  
 z  | Number | 元素的z-index | 0
 left | Number | 元素初始Left | 0
 top | Number | 元素初始Top | 0
 active | Boolean | 激活状态 active state | false
 grid | Array | 网格模式 Grid mode | [0, 0]
 parent | Boolean | 是否以父元素为参照目标，否则为document | false
 resizable | Boolean | 是否启用缩放功能 Whether to enable the zoom function | true
 resizeInstance | Boolean | 是否计算缩放按钮值 Whether to calculate the zoom button value | false
 minWidth | Number | 最小宽度 min width | 0
 minHeight | Number | 最小高度 min Height | 0
 maxHeight | Number | 最大高度 max height | 0
 maxWidth | Number | 最大宽度 max width | 0
 moveEmitDelay | Number | 元素移动事件的节流阈值 Throttle threshold for element movement events | 0
 resizeEmitDelay | Number | 元素缩放事件的节流阈值 Throttle threshold for element zoom event | 0
 draggable | Boolean | 是否启用拖拽 Whether to enable drag and drop | true
 initWidth | Number | 初始宽度 init width | 0
 initHeight | Number | 初始高度 init height | 0
 dragHandle | String | 允许触发拖拽的元素类名 The class name of the element that is allowed to trigger the drag | ''
 dragFilter | String | 不允许出发拖拽的元素类名 The class name of the element that is not allowed to start dragging | ''
 disableUserSelect | Boolean | 是否允许用户选择内容 Whether to allow users to select content | false
 componentData | Object | 绑定的数据值，会通过move，resize等事件给回传过来 The bound data value will be passed back through events such as move and resize | {}
 helpLine | Boolean | 辅助线 help line | false
 lineClass | String | 辅助线的类名 line class name | ''
 handles | Array | ['tl', 'tm', 'tr', 'ml', 'mr', 'bl', 'bm', 'br'] | [all]
 axis | String | 允许操作方向 Allow operation direction  value: x,y,xy | 'xy',
 draggingClass | String | 拖动时的class Class when dragging | ''
 resizingClass | String | 缩放时的class Class when zooming | ''
 resizeClass | String | 缩放控件的class Zoom control class | ''
 
#### 反馈 Feedback
 > 916411582@qq.com

##### Design By SXX