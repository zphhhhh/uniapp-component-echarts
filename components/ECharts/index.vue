<template>
    <canvas
        v-if="canvasId"
        class="e-charts"
        :id="canvasId"
        :canvasId="canvasId"
        :style="{width, height}"
        @touchstart="touchStart"
        @touchmove="touchMove"
        @touchend="touchEnd"
        @error="error"
    ></canvas>
</template>

<script>
/**
 * echarts 封装，基于 [echarts-for-weixin 微信小程序](https://github.com/ecomfe/echarts-for-weixin)
 * @author zhangpenghe
 * @date 2019-10-25
 * @param {Object} option 必需，作为 echarts 的 option，见 [官方文档](https://www.echartsjs.com/zh/tutorial.html#5%20分钟上手%20ECharts)
 * @param {string} canvasId 必需，作为小程序 canvas 原生属性，需保证唯一性，但可随意填写
 * @param {string} width 可选，作为 canvas 的 width
 * @param {string} height 可选，作为 canvas 的 height
 * @example
 *          <e-charts canvas-id="zphhhhh" :option="option" width="100%" height="600px"></e-charts>
 */
import WxCanvas from './wx-canvas';
import * as echarts from 'echarts/dist/echarts.min';

function wrapTouch(e) {
    for (let i = 0; i < e.mp.touches.length; i += 1) {
        const touch = e.mp.touches[i];
        touch.offsetX = touch.x;
        touch.offsetY = touch.y;
    }
    return e;
}

export default {
    name: 'ECharts',

    props: {
        /**
         *  echarts 的 option，会透传给 echarts
         */
        option: {
            required: true,
            type: Object
        },
        /**
         * canvas id，要保证全局唯一性，小程序要求，否则会出问题
         */
        canvasId: {
            required: true,
            type: String,
            default: 'my-echarts'
        },
        /**
         * echarts 宽
         */
        width: {
            type: String,
            default: '100%'
        },
        /**
         * echarts 高
         */
        height: {
            type: String,
            default: '100%'
        }
    },

    onReady() {
        this.init();
    },

    methods: {
        init() {
            this.ctx = uni.createCanvasContext(this.canvasId, this);

            const canvas = new WxCanvas(this.ctx, this.canvasId);
            const query = uni.createSelectorQuery().in(this);

            echarts.setCanvasCreator(() => canvas);

            query
                .select(`#${this.canvasId}`)
                .boundingClientRect(res => {
                    if (!res) {
                        setTimeout(() => this.init(), 50);
                        return;
                    }

                    const chart = echarts.init(canvas, null, {
                        width: res.width,
                        height: res.height
                    });

                    chart.setOption(this.option);

                    const {handler} = chart.getZr();

                    this.handler = handler;
                    this.processGesture = handler.proxy.processGesture || (() => {});
                })
                .exec();
        },
        canvasToTempFilePath(opt) {
            const {canvasId} = this;
            this.ctx.draw(true, () => {
                uni.canvasToTempFilePath({
                    canvasId,
                    ...opt
                });
            });
        },
        touchStart(e) {
            if (!e.mp.touches.length || !this.handler) return;
            const touch = e.mp.touches[0];
            this.handler.dispatch('mousedown', {
                zrX: touch.x,
                zrY: touch.y
            });
            this.handler.dispatch('mousemove', {
                zrX: touch.x,
                zrY: touch.y
            });
            this.processGesture(wrapTouch(e), 'start');
        },
        touchMove(e) {
            if (!e.mp.touches.length || !this.handler) return;

            const touch = e.mp.touches[0];
            this.handler.dispatch('mousemove', {
                zrX: touch.x,
                zrY: touch.y
            });
            this.processGesture(wrapTouch(e), 'change');
        },
        touchEnd(e) {
            if (!this.handler) return;

            const touch = e.mp.changedTouches ? e.mp.changedTouches[0] : {};
            this.handler.dispatch('mouseup', {
                zrX: touch.x,
                zrY: touch.y
            });
            this.handler.dispatch('click', {
                zrX: touch.x,
                zrY: touch.y
            });
            this.processGesture(wrapTouch(e), 'end');
        }
    }
};
</script>
