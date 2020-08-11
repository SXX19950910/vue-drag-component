<template>
    <div id="limit-warp">
        <div class="left-box">
            <pre v-highlightjs>
                <code class="html">
                    {{ code }}
                </code>
            </pre>
            <pre v-highlightjs>
                <code class="css">
                    {{ styleCode }}
                </code>
            </pre>
        </div>
        <div class="right-box">
            <vue-drag-component :parent="true" :init-height="100" :init-width="100" :max-height="400" :max-width="400" :min-width="100" :min-height="100" :top="200" :left="200" :active.sync="active">
                <div class="box"></div>
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
                code: '\n<vue-drag-component :parent="true" :init-height="100" :init-width="100" :max-height="400" :max-width="400" :min-width="100" :min-height="100" :top="200" :left="200" :active.sync="active">\n' +
                    '    <div class="box"></div>\n' +
                    '</vue-drag-component>',
                styleCode: ''
            }
        }
    }
</script>

<style lang="scss">
    #limit-warp {
        height: 100%;
        display: flex;
        .box {
            width: 100%;
            height: 100%;
            border: 1px dashed #333;
        }
    }
</style>