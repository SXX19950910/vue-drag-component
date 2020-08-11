<template>
    <div id="grid-warp">
        <div class="left-box">
            <pre v-highlightjs>
                <code class="html">
                    {{ code }}
                </code>
            </pre>
        </div>
        <div class="right-box">
            <vue-drag-component :parent="true" :grid="[80, 80]" :init-width="80" :init-height="80" :max-width="240" :max-height="240" :active.sync="active">
                <div class="box" />
            </vue-drag-component>
        </div>
    </div>
</template>

<script>
    import vueDragComponent from '../../components/drag'
    export default {
        components: {
            vueDragComponent
        },
        data() {
            return {
                active: false,
                code: '\n<vue-drag-component :parent="true" :grid="[80, 80]" :init-width="80" :init-height="80" :max-width="240" :max-height="240" :active.sync="active">\n' +
                    '    <div class="box" />\n' +
                    '</vue-drag-component>'
            }
        },
        methods: {
        },
        mounted() {
        }
    }
</script>

<style lang="scss">
    #grid-warp {
        height: 100%;
        display: flex;
        .right-box {
            border-top: none !important;
            border-bottom: none !important;
            background: linear-gradient(-90deg, rgba(0, 0, 0, 0.1) 1px, transparent 1px) 0% 0% / 40px 40px, linear-gradient(rgba(0, 0, 0, 0.1) 1px, transparent 1px) 0% 0% / 40px 40px;
            background-color: white;
            .box {
                border: 1px dashed #333;
                height: 100%;
                width: 100%;
            }
        }
    }
</style>