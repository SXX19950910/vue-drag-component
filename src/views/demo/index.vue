<template>
    <div id="demo-warp">
        <div class="menu-area">
            <div v-for="(item, index) in list" :key="index" class="item" :class="activeName === item.name ? 'active' : ''" @click="handleSetClick(item.name)">
                {{ item.name }}
            </div>
        </div>
        <div class="view-area">
            <component :is="activeComponent"></component>
        </div>
    </div>
</template>

<script>
    import basicGuide from './../basic/index.vue'
    import gridGuide from './../grid/index.vue'
    import initGuide from './../init/index.vue'
    import limitGuide from './../limit/index.vue'
    import usuallyGuide from './../usually/index.vue'
    import controlGuide from './../control/index.vue'
    export default {
        components: {
            basicGuide,
            gridGuide,
            initGuide,
            limitGuide,
            usuallyGuide,
            controlGuide
        },
        data() {
            return {
                activeName: '',
                list: [
                    {
                        name: '基础用法'
                    },
                    {
                        name: '网格'
                    },
                    {
                        name: '初始化坐标/宽高'
                    },
                    {
                        name: '最大值/最小值'
                    },
                    {
                        name: '常用属性'
                    },
                    {
                        name: '控制器'
                    }
                ]
            }
        },
        computed: {
            activeComponent() {
                const result = {
                    '基础用法': 'basicGuide',
                    '网格': 'gridGuide',
                    '初始化坐标/宽高': 'initGuide',
                    '最大值/最小值': 'limitGuide',
                    '常用属性': 'usuallyGuide',
                    '控制器': 'controlGuide'
                }
                return result[this.activeName]
            }
        },
        methods: {
            handleSetClick(name) {
                this.activeName = name
            }
        }
    }
</script>

<style lang="scss">
    #demo-warp {
        display: flex;
        align-items: center;
        flex-direction: column;
        padding: 0 15px 15px 15px;
        .menu-area {
            width: 100%;
            height: 40px;
            display: flex;
            align-items: center;
            padding: 0 20px;
            font-size: 14px;
            .item {
                cursor: pointer;
                text-decoration: underline;
                margin-right: 20px;
                &.active {
                    color: #DC4E41;
                }
            }
        }
        .view-area {
            height: calc(100vh - 55px);
            width: 100%;
            .left-box {
                width: 50%;
                border-left: 1px solid #dedede;
                padding: 15px;
                background-color: #f8f8f8;
                border-top: 1px solid #dedede;
                overflow: auto;
                border-bottom: 1px solid #dedede;
            }
            .right-box {
                width: 50%;
                border: 1px solid #dedede;
            }
        }
    }
</style>