<template>
    <div id="control-warp">
        <div class="left-box">
            <label>
                选择控制器：
                <select v-model="handle" @change="onChange">
                    <option value="one">控制器1</option>
                    <option value="two">控制器2</option>
                    <option value="three">控制器3</option>
                </select>
            </label>

            <pre v-highlightjs="code">
                <code class="html">
                    {{ code }}
                </code>
            </pre>
        </div>
        <div class="right-box">
            <vue-drag-component parent :drag-handle="handle" :init-height="300" :init-width="300">
                <div class="box">
                    <div class="circle-handle one">1</div>
                    <div class="circle-handle two">2</div>
                    <div class="circle-handle three">3</div>
                </div>
            </vue-drag-component>
        </div>
    </div>
</template>

<script>
    import vueDragComponent from './../../components/drag'
    export default {
        components: {
          vueDragComponent
        },
        data() {
            return {
                handle: 'one',
                code: '\n<vue-drag-component parent drag-handle="one" :init-height="300" :init-width="300">\n' +
                    '      <div class="box">\n' +
                    '           <div class="circle-handle one">1</div>\n' +
                    '           <div class="circle-handle two">2</div>\n' +
                    '           <div class="circle-handle three">3</div>\n' +
                    '       </div>\n' +
                    '</vue-drag-component>'
            }
        },
        methods: {
            onChange(content) {
                console.log(content.target.value)
                this.$set(this, 'code', `\n<vue-drag-component parent drag-handle="${content.target.value}" :init-height="300" :init-width="300">\n` +
                    '     <div class="box">\n' +
                    '           <div class="circle-handle one">1</div>\n' +
                    '           <div class="circle-handle two">2</div>\n' +
                    '           <div class="circle-handle three">3</div>\n' +
                    '     </div>\n' +
                    `</vue-drag-component>`)
            }
        }
    }
</script>

<style lang="scss">
    #control-warp {
        height: 100%;
        display: flex;
        .box {
            width: 100%;
            height: 100%;
            border: 1px dashed #333333;
            .circle-handle {
                border-radius: 100%;
                text-align: center;
                display: flex;
                align-items: center;
                justify-content: center;
                color: white;
                position: absolute;
                &:nth-child(1) {
                    width: 40px;
                    height: 40px;
                    background-color: #DC4E41;
                    top: 50px;
                    left: 50px;
                }
                &:nth-child(2) {
                    width: 40px;
                    height: 40px;
                    background-color: #FFBE00;
                    top: 150px;
                    left: 120px;
                }
                &:nth-child(3) {
                    width: 40px;
                    height: 40px;
                    background-color: green;
                    top: 90px;
                    left: 190px;
                }
            }
        }
    }
</style>