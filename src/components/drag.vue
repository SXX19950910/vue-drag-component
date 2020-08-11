<template>
    <div ref="drag" class="vue-drag-warp" :style="dragStyle" :class="dragClass" @mousedown.stop.prevent="handleMouseDown" @contextmenu="handleContextMenu">
        <slot />
        <drag-line v-if="lineVisible" :class="lineClass" />
        <drag-resize v-if="resizeVisible" ref="resize" :class="resizeClass" :handles="handles" @resize-tl="onResizeTlDown" @resize-tr="onResizeTrDown" @resize-bl="onResizeBlDown" @resize-br="onResizeBrDown" @resize-mr="onResizeMrDown" @resize-bm="onResizeBmDown" @resize-ml="onResizeMlDown" @resize-tm="onResizeTmDown"/>
    </div>
</template>

<script>
    import {on, off} from '../utils/dom';
    import dragResize from './resize';
    import dragLine from './line'
    import { throttle, debounce } from 'throttle-debounce';
    import _ from 'loadsh';
    export default {
        name: 'Drag',
        components: {
            dragResize,
            dragLine
        },
        props: {
            z: {
              type: Number,
              default: 0
            },
            left: {
              type: Number,
              default: 0
            },
            top: {
                type: Number,
                default: 0
            },
            active: {
              type: Boolean,
              default: false
            },
            parent: {
                type: Boolean,
                default: false
            },
            resizable: {
                type: Boolean,
                default: true
            },
            resizeInstance: {
                type: Boolean,
                default: false
            },
            minWidth: {
                type: Number,
                default: 0
            },
            minHeight: {
                type: Number,
                default: 0
            },
            maxHeight: {
                type: Number,
                default: 0
            },
            maxWidth: {
                type: Number,
                default: 0
            },
            moveEmitDelay: {
                type: Number,
                default: 0
            },
            resizeEmitDelay: {
                type: Number,
                default: 0
            },
            draggable: {
                type: Boolean,
                default: true
            },
            initWidth: {
                type: Number,
                default: 0
            },
            initHeight: {
                type: Number,
                default: 0
            },
            dragHandle: {
                type: String,
                default: ''
            },
            dragFilter: {
                type: String,
                default: ''
            },
            disableUserSelect: {
              type: Boolean,
              default: true
            },
            componentData: {
                type: Object,
                default() {
                    return {}
                }
            },
            helpLine: {
                type: Boolean,
                default: false
            },
            lineClass: {
              type: String,
              default: ''
            },
            handles: {
                type: Array,
                default() {
                    return ['tl', 'tm', 'tr', 'ml', 'mr', 'bl', 'bm', 'br']
                },
                validator(value) {
                    let result = []
                    const list = ['tl', 'tm', 'tr', 'ml', 'mr', 'bl', 'bm', 'br']
                    if (value && value.length > 0) {
                        value.map(item => {
                            const current = list.find(tag => tag === item)
                            current && result.push(current)
                        })
                    }
                    return result
                }
            },
            // 可拖动的坐标轴
            axis: {
                type: String,
                default: 'xy',
                validator(value) {
                    const reg = ['x', 'y', 'xy']
                    let result = value
                    if (!reg.includes(result)) {
                        console.error('The value must be one of x, y, xy')
                        result = 'xy'
                    }
                    return result
                }
            },
            grid: {
                type: Array,
                default() {
                    return [0, 0]
                }
            },
            draggingClass: {
                type: String,
                default: 'dragging'
            },
            resizingClass: {
                type: String,
                default: 'resizing'
            },
            resizeClass: {
                type: String,
                default: ''
            }
        },
        data() {
            return {
                x: '',
                y: '',
                downX: '',
                downY: '',
                resizeDownX: '',
                resizeDownY: '',
                downWidth: '',
                downHeight: '',
                offsetLeft: '',
                offsetTop: '',
                downOffsetLeft: '',
                downOffsetTop: '',
                resizeOffsetRight: '',
                resizeOffsetBottom: '',
                defaultWidth: '',
                defaultHeight: '',
                width: '',
                height: '',
                drag: {},
                flag: '',
                board: {},
                resize: {},
                moveInput: Function,
                resizeInput: Function,
                throttleMove: Function,
                windowResize: Function,
                dragging: false,
                resizing: false
            };
        },
        destroyed() {
            this.clearAllListener();
        },
        computed: {
            lineVisible() {
                return (this.dragging || this.resizing) && this.helpLine
            },
            resizeVisible() {
                return this.active && this.resizable
            },
            dragStyle() {
                const {width, height, y, x, disableUserSelect, z} = this;
                return {
                    width: `${width}px`,
                    height: `${height}px`,
                    // transform: `translate(${x}px, ${y}px)`,
                    top: `${y}px`,
                    left: `${x}px`,
                    userSelect: disableUserSelect ? 'none' : 'unset',
                    zIndex: z || 'unset'
                };
            },
            dragClass() {
                let result = []
                const { flag, active, dragging, resizing, draggingClass, resizingClass } = this
                const className = `${flag}-drag`
                flag && result.push(className)
                active && result.push('is-active')
                dragging && result.push(draggingClass)
                resizing && result.push(resizingClass)
                return result
            },
            isX() {
                return this.axis === 'xy' || this.axis === 'x'
            },
            isY() {
                return this.axis === 'xy' || this.axis === 'y'
            }
        },
        created() {
        },
        mounted() {
            this.init();
        },
        methods: {
            init() {
                this.handleSetRect();
                const { top, left, width, height } = this.drag;
                const { initWidth, initHeight } = this;
                this.offsetLeft = left;
                this.offsetTop = top;
                if (this.left > 0) this.x = this.left + left;
                if (this.top > 0) this.y = this.top + top;
                this.defaultHeight = height;
                this.defaultWidth = width;
                this.moveInput = throttle(this.moveEmitDelay, this.handleInputMove);
                this.resizeInput = throttle(this.resizeEmitDelay, this.handleInputResize);
                this.throttleMove = throttle(200, this.handleMouseMove);
                this.windowResize = debounce(200, this.initBasicData)
                on(window, 'resize', this.windowResize);
                if (initWidth > 0) this.width = this.initWidth;
                if (initHeight > 0) this.height = this.initHeight;
                this.validatorProps();
            },
            generateId() {
                return Number(Math.random().toString().substr(3, length) + Date.now()).toString(36);
            },
            validatorProps() {
                const { initWidth, minWidth, initHeight, minHeight } = this;
                if (initWidth > 0 && initWidth < minWidth) {
                    console.warn('The initial width cannot be less than the minimum width')
                }
                if (initHeight > 0 && initHeight < minHeight) {
                    console.warn('The initial height cannot be less than the minimum height')
                }
            },
            handleSetRect() {
                const $resize = this.$refs.resize
                this.board = this.parent ? this.$refs.drag.parentElement.getBoundingClientRect() : document.body.getBoundingClientRect();
                if (this.resizable && this.active && $resize) this.resize = $resize.$el.querySelectorAll('.handle')[0].getBoundingClientRect();
                this.drag = this.$refs.drag.getBoundingClientRect();
            },
            handleInputMove(e) {
                const {width, height, x, y} = this;
                const drag = {
                    width,
                    height,
                    x,
                    y
                };
                this.$emit('dragging', e, drag);
            },
            handleInputResize(e) {
                const {width, height, x, y} = this;
                const drag = {
                    width,
                    height,
                    x,
                    y
                };
                this.$emit('resizing', e, drag);
            },
            initLayoutScheme(e) {
                this.$nextTick(() => {
                    this.handleSetRect();
                })
                const $drag = e.path.find((item) => item.className.includes('drag-warp'));
                const {top, left, width, height} = $drag.getBoundingClientRect();
                this.defaultHeight = height;
                this.defaultWidth = width;
                this.downX = e.clientX - left;
                this.downY = e.clientY - top;
                this.downOffsetLeft = left;
                this.downOffsetTop = top;

            },
            initBasicData() {
                this.handleSetRect()
                const {top, left, width, height} = this.$refs.drag.getBoundingClientRect();
                this.defaultHeight = height;
                this.defaultWidth = width;
                this.downOffsetLeft = left;
                this.downOffsetTop = top;
            },
            clearAllListener() {
                off(document, 'mouseup', this.handleResizeUp);
                off(document, 'mouseup', this.handleMouseUp);
                off(document, 'mousemove', this.handleMouseMove);
                off(document, 'mousemove', this.handleResizeBrMove);
                off(document, 'mousemove', this.handleResizeMrMove);
                off(document, 'mousemove', this.handleResizeBmMove);
                off(document, 'mousemove', this.handleResizeMlMove);
                off(document, 'mousemove', this.handleResizeTmMove);
                off(document, 'mousemove', this.handleResizeBlMove);
                off(document, 'mousemove', this.handleResizeTrMove);
                off(document, 'mousemove', this.handleResizeTlMove);
            },
            handleSetActive(e) {
                this.$emit('update:active', true)
                this.$emit('active', e, _.cloneDeep(this.componentData))
            },
            handleMouseDown(e) {
                const targetClass = this.dragHandle;
                const filterClass = this.dragFilter;
                const currentClass = e.path[0].className;
                const nonActive = !this.active;
                const isTarget = targetClass === '' || currentClass.includes(targetClass);
                const nonFilter = filterClass === '' || !currentClass.includes(filterClass);
                nonActive && this.handleSetActive(e);
                if (isTarget && nonFilter) {
                    this.initLayoutScheme(e);
                    this.draggable && on(document, 'mousemove', this.handleMouseMove);
                    on(document, 'mouseup', this.handleMouseUp);
                }
            },
            handleContextMenu() {
                this.clearAllListener();
            },
            handleMouseMove(e) {
                const {parent, downX, downY, resizeInstance, grid, downOffsetLeft, downOffsetTop} = this;
                const resizeBarWidth = resizeInstance ? this.resize.width : 0;
                const resizeBarHeight = resizeInstance ? this.resize.height : 0;
                const offsetLeft = parent ? this.offsetLeft : 0;
                const offsetTop = parent ? this.offsetTop : 0;
                const width = this.defaultWidth;
                const height = this.defaultHeight;
                const clientX = e.clientX;
                const clientY = e.clientY;
                const boardHeight = this.board.height;
                const boardWidth = this.board.width;
                const x = clientX - downX;
                const y = clientY - downY;
                const isMaxLeft = (width + x) - offsetLeft >= (boardWidth - resizeBarWidth);
                const isMaxTop = (height + y) - offsetTop >= (boardHeight - resizeBarHeight);
                const isMinLeft = x - offsetLeft <= 0;
                const isMinTop = y - offsetTop - resizeBarHeight <= 0;
                const maxLeft = boardWidth - width + offsetLeft - resizeBarWidth;
                const minLeft = offsetLeft + resizeBarWidth;
                const maxTop = boardHeight - height + offsetTop - resizeBarHeight;
                const minTop = offsetTop + resizeBarHeight;
                const left = grid[0] ? Math.round(x / grid[0]) * grid[0] : x;
                const top = grid[1] ? Math.round(y / grid[1]) * grid[1] : y;
                if (this.isX) {
                    if (isMinLeft) {
                        this.x = minLeft;
                    } else if (isMaxLeft) {
                        this.x = maxLeft;
                    } else {
                        this.x = left;
                    }
                }
                if (this.isY) {
                    if (isMinTop) {
                        this.y = minTop;
                    } else if (isMaxTop) {
                        this.y = maxTop;
                    } else {
                        this.y = top;
                    }
                }
                this.dragging = downOffsetLeft !== this.x || downOffsetTop !== this.y;
                this.moveInput(e);
            },
            handleMouseUp(e) {
                off(document, 'mousemove', this.handleMouseMove);
                off(document, 'mouseup', this.handleMouseUp);
                this.onDragStop(e);
            },
            onDragStop(e) {
                this.dragging = false;
                e.drag = {
                    x: this.x,
                    y: this.y
                };
                this.$emit('drag-stop', e, _.cloneDeep(this.componentData));
            },
            onResizeStop(e) {
                this.resizing = false;
                e.drag = {
                    x: this.x,
                    y: this.y,
                    width: this.width,
                    heightL: this.height
                };
                this.$emit('resize-stop', e, _.cloneDeep(this.componentData))
            },
            handleResizeUp(e) {
                off(document, 'mousemove', this.handleResizeBrMove);
                off(document, 'mousemove', this.handleResizeMrMove);
                off(document, 'mousemove', this.handleResizeBmMove);
                off(document, 'mousemove', this.handleResizeMlMove);
                off(document, 'mousemove', this.handleResizeTmMove);
                off(document, 'mousemove', this.handleResizeBlMove);
                off(document, 'mousemove', this.handleResizeTrMove);
                off(document, 'mousemove', this.handleResizeTlMove);
                off(document, 'mouseup', this.handleResizeUp);
                this.onResizeStop(e);
            },
            handleResizeBrMove(e) {
                this.handleResizeMrMove(e)
                this.handleResizeBmMove(e)
                this.resizeInput(e);
            },
            handleResizeMlMove(e) {
                const {grid, downWidth, downOffsetLeft, minWidth, resizeDownX, downHeight, board, resize, resizeInstance, maxWidth} = this;
                const clientX = e.clientX;
                const dir = resizeInstance ? resize.width : 0;
                const moveX = clientX - resizeDownX;
                const boardLeft = board.left;
                const isReverseX = moveX < 0;
                const width = downWidth + moveX;
                const absWidth = downWidth + Math.abs(moveX);
                const diffWidth = downWidth - moveX;
                const diffLeft = downOffsetLeft + moveX;
                const isEdgeLeft = isReverseX && (Math.abs(moveX) + boardLeft) >= (downOffsetLeft - dir);
                const edgeWidth = downWidth + (downOffsetLeft - boardLeft) - dir;
                const maxLeft = downOffsetLeft + downWidth - minWidth;
                const edgeLeft = 0 + boardLeft + dir;
                const isMinWidth = diffWidth <= minWidth;
                const left = downOffsetLeft - Math.abs(moveX)
                const addComputedLeft = grid[0] ? Math.round(Math.abs(parseFloat(diffLeft)) / grid[0]) * grid[0] : diffLeft;
                const addComputedWidth = grid[0] ? downWidth - Math.round(moveX / grid[0]) * grid[0] : diffWidth;
                const diffComputedWidth = grid[0] ? downWidth - Math.round(moveX / grid[0]) * grid[0] : absWidth;
                const diffComputedLeft = grid[0] ? Math.round(Math.abs(left) / grid[0]) * grid[0] : left;
                const isMaxWidth = maxWidth > 0 && absWidth >= maxWidth;
                const computedMaxLeft = downOffsetLeft - maxWidth + downWidth;
                if (isEdgeLeft) {
                    this.width = edgeWidth;
                    this.x = edgeLeft;
                } else if (isReverseX) {
                    if (isMaxWidth) {
                        this.width = maxWidth;
                        this.x = computedMaxLeft;
                    } else {
                        this.width = diffComputedWidth;
                        this.x = diffComputedLeft;
                    }
                } else if (!isReverseX) {
                    if (isMinWidth) {
                        this.width = minWidth;
                        this.x = maxLeft;
                    } else {
                        this.width = addComputedWidth;
                        this.x = addComputedLeft;
                    }
                } else {
                    this.width = width;
                    this.x = downOffsetLeft;
                }
                this.resizing = downWidth !== this.width || downHeight !== this.height ||  resizeDownX !== clientX;
                this.resizeInput(e);
            },
            handleResizeMrMove(e) {
                const {clientX} = e;
                const {downWidth, downHeight, resizeDownX, maxWidth, resizeInstance, resize} = this;
                const grid = this.grid;
                const dir = resizeInstance ? resize.width : 0;
                const minWidth = this.minWidth === 0 ? width : this.minWidth;
                const moveX = clientX - this.resizeDownX;
                const offsetRight = this.resizeOffsetRight - dir;
                const width = downWidth + moveX;
                const widthLimit = width <= minWidth;
                const isMaxWidth = (maxWidth > 0) && (width >= maxWidth);
                const xEdge = moveX >= offsetRight;
                const edgeWidth = downWidth + offsetRight;
                const computedWidth = grid[0] ? Math.round(moveX / grid[0]) * grid[0] + downWidth : width;
                if (widthLimit) {
                    this.width = minWidth;
                } else if (isMaxWidth) {
                    this.width = maxWidth;
                } else if (xEdge) {
                    this.width = edgeWidth;
                } else {
                    this.width = computedWidth;
                }
                this.resizing = downWidth !== this.width || downHeight !== this.height ||  resizeDownX !== clientX;
                this.resizeInput(e);
            },
            onResizeMrDown(e) {
                this.initResizeData(e);
                on(document, 'mousemove', this.handleResizeMrMove);
                on(document, 'mouseup', this.handleResizeUp);
                e.stopPropagation();
            },
            handleResizeTmMove(e) {
                const {downHeight, downOffsetTop, resizeDownY, resizeInstance, grid, minHeight, board, resizeDownX, downWidth, resize, maxHeight} = this;
                const clientY = e.clientY;
                const clientX = e.clientX;
                const moveY = clientY - resizeDownY;
                const dir = resizeInstance ? resize.height : 0
                const boardTop = board.top;
                const isReverseY = moveY < 0;
                const height = downHeight + moveY;
                const absHeight = downHeight + Math.abs(moveY);
                const diffHeight = downHeight - moveY;
                const diffTop = downOffsetTop + moveY;
                const isEdgeTop = isReverseY && (Math.abs(moveY) + boardTop) >= (downOffsetTop - dir);
                const edgeHeight = (downHeight + (downOffsetTop - boardTop) - dir);
                const maxTop = downOffsetTop + downHeight - minHeight;
                const isMinHeight = diffHeight <= minHeight;
                const top = downOffsetTop - Math.abs(moveY);
                const diffComputedTop = grid[1] ? Math.round(Math.abs(parseFloat(diffTop)) / grid[1]) * grid[1] : diffTop;
                const diffComputedHeight = grid[1] ? downHeight - Math.round(moveY / grid[1]) * grid[1] : diffHeight;
                const addComputedHeight = grid[1] ? downHeight - Math.round(moveY / grid[1]) * grid[1] : absHeight;
                const addComputedTop = grid[1] ? Math.round(Math.abs(top) / grid[1]) * grid[1] : top;
                const isMaxHeight = maxHeight > 0 && absHeight >= maxHeight;
                const computedMaxTop = downOffsetTop - maxHeight + downHeight;
                const edgeTop = 0 + boardTop + dir;
                if (isEdgeTop) {
                    let height = edgeHeight <= maxHeight ? edgeHeight : maxHeight;
                    let y = edgeTop <= computedMaxTop ? computedMaxTop : edgeTop;
                    if (maxHeight === 0) {
                        height = edgeHeight;
                        y =  edgeTop;
                    }
                    this.height = height;
                    this.y = y;
                } else if (isReverseY) {
                    if (isMaxHeight) {
                        this.height = maxHeight;
                        this.y = computedMaxTop;
                    } else {
                        this.height = addComputedHeight;
                        this.y = addComputedTop;
                    }
                } else if (!isReverseY) {
                    if (isMinHeight) {
                        this.height = minHeight;
                        this.y = maxTop;
                    } else {
                        this.height = diffComputedHeight;
                        this.y = diffComputedTop;
                    }
                } else {
                    this.height = height;
                    this.y = downOffsetTop;
                }
                this.resizing = downWidth !== this.width || downHeight !== this.height ||  resizeDownX !== clientX;
                this.resizeInput(e);
            },
            handleResizeBlMove(e) {
                this.handleResizeMlMove(e);
                this.handleResizeBmMove(e);
            },
            handleResizeTrMove(e) {
                this.handleResizeTmMove(e);
                this.handleResizeMrMove(e);
            },
            handleResizeTlMove(e) {
                this.handleResizeTmMove(e);
                this.handleResizeMlMove(e);
            },
            handleResizeBmMove(e) {
                const {clientY, clientX} = e;
                const {downHeight, resizeDownX, downWidth, minHeight, resizeOffsetBottom, resizeDownY, grid, maxHeight, resizeInstance, resize} = this;
                const dir = resizeInstance ? resize.width : 0
                const moveY = clientY - resizeDownY;
                const offsetBottom = resizeOffsetBottom - dir;
                const height = downHeight + moveY;
                const heightLimit = height <= minHeight;
                const yEdge = moveY >= offsetBottom;
                const edgeHeight = downHeight + offsetBottom;
                const isMaxHeight = (maxHeight > 0) && (height >= maxHeight);
                const computedHeight = grid[0] ? Math.round(moveY / grid[1]) * grid[1] + downHeight - dir : height;
                if (heightLimit) {
                    this.height = minHeight;
                } else if (isMaxHeight) {
                    this.height = maxHeight;
                } else if (yEdge) {
                    this.height = edgeHeight;
                } else {
                    this.height = computedHeight;
                }
                this.resizing = downWidth !== this.width || downHeight !== this.height ||  resizeDownX !== clientX;
                this.resizeInput(e);
            },
            onResizeTlDown(e) {
                this.initResizeData(e);
                on(document, 'mousemove', this.handleResizeTlMove);
                on(document, 'mouseup', this.handleResizeUp);
                e.stopPropagation();
            },
            onResizeTrDown(e) {
                this.initResizeData(e);
                on(document, 'mousemove', this.handleResizeTrMove);
                on(document, 'mouseup', this.handleResizeUp);
                e.stopPropagation();
            },
            onResizeBlDown(e) {
                this.initResizeData(e);
                on(document, 'mousemove', this.handleResizeBlMove);
                on(document, 'mouseup', this.handleResizeUp);
                e.stopPropagation();
            },
            onResizeBmDown(e) {
                this.initResizeData(e);
                on(document, 'mousemove', this.handleResizeBmMove);
                on(document, 'mouseup', this.handleResizeUp);
                e.stopPropagation();
            },
            onResizeMlDown(e) {
                this.initResizeData(e);
                on(document, 'mousemove', this.handleResizeMlMove);
                on(document, 'mouseup', this.handleResizeUp);
                e.stopPropagation();
            },
            onResizeTmDown(e) {
                this.initResizeData(e);
                on(document, 'mousemove', this.handleResizeTmMove);
                on(document, 'mouseup', this.handleResizeUp);
                e.stopPropagation();
            },
            initResizeData(e) {
                this.handleSetRect();
                const board = this.board;
                const {width, height, left, top} = this.drag;
                this.resizeOffsetRight = board.width - (left - board.left) - width;
                this.resizeOffsetBottom = board.height - (top - board.top) - height;
                this.resizeDownX = e.clientX;
                this.resizeDownY = e.clientY;
                this.downWidth = width;
                this.downHeight = height;
                this.downOffsetTop = top;
                this.downOffsetLeft = left;
            },
            onResizeBrDown(e) {
                this.initResizeData(e);
                on(document, 'mousemove', this.handleResizeBrMove);
                on(document, 'mouseup', this.handleResizeUp);
                e.stopPropagation();
            }
        },
    };
</script>

<style lang="scss">
    .vue-drag-warp {
        position: absolute;
        cursor: default;
        color: #000;
        max-width: 100%;
        max-height: 100%;
    }
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
