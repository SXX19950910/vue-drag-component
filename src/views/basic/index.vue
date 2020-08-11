<template>
    <div id="basic-warp">
        <div class="left-box">
            <pre v-highlightjs>
                <code class="html">
                    {{ code }}
                </code>
            </pre>
        </div>
        <div class="right-box">
            <vue-drag-component :parent="true" :active.sync="active">
                <div class="box"></div>
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
                code: '\n<vue-drag-component :parent="true" :active.sync="active">\n' +
                    '   <div class="box"></div>\n' +
                    '</vue-drag-component>'
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