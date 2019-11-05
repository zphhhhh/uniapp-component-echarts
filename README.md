# uniapp-component-echarts

封装了 echarts 的 uniapp 组件，可能是目前 Github 中使用最简单最容易理解的封装，基于 [echarts-for-weixin 微信小程序](https://github.com/ecomfe/echarts-for-weixin)。

### 安装

1. `npm i echarts uniapp-component-echarts` 
2. 拷贝 `uniapp-component-echarts/components/ECharts` 到你的项目的 `components` 目录。

### 示例

> Tip1: echarts 完整压缩包仍然很大（~760KB），而小程序一般限制编译后体积不超过 2M，示例仅为方便，建议实际开发时使用定制版 echarts。

> Tip2: 仅在 微信小程序、百度小程序 测试通过，其他端请自行验证

``` html
<template>
    <e-charts canvas-id="zphhhhh" :option="option" width="100%" height="600px" />
</template>

<script>
import ECharts from '@/components/ECharts';

export default {
    components: { ECharts },

    data() {
        return {
            option: {
                legend: {
                    orient: 'horizontal',
                    bottom: 10,
                    data: ['0-10万', '10-100万', '100-1000万', '1000-5000万', '5000万以上']
                },
                series: [{
                    type: 'pie',
                    radius: '36%',
                    minAngle: 5,
                    selectedOffset: 5,
                    data: [
                        {
                            value: 600000000000,
                            name: '5000万以上',
                            selected: false
                        },
                        {
                            value: 18000000,
                            name: '1000-5000万',
                            selected: true,
                            label: {
                                normal: {
                                    textStyle: {
                                        color: '#ff9000'
                                    },
                                    formatter: '\n{b}\n(共{c}万)\n当前企业地位'
                                }
                            }
                        },
                        {
                            value: 1200000,
                            name: '100-1000万',
                            selected: false
                        },
                        {
                            value: 110000,
                            name: '10-100万',
                            selected: false
                        },
                        {
                            value: 1200,
                            name: '0-10万',
                            selected: false
                        }
                    ],
                    markPoint: {
                        symbol: 'pin',
                        label: {
                            normal: {
                                show: 1
                            }
                        }
                    },
                    label: {
                        color: '#666',
                        fontSize: 11,
                        formatter: '\n{b}\n(共{c}万)\n'
                    },
                    labelLine: {
                        normal: {
                            lineStyle: {
                                color: '#666'
                            },
                            length: 20,
                            length2: 20
                        }
                    },
                    itemStyle: {
                        emphasis: {
                            shadowBlur: 10,
                            shadowOffsetX: 0,
                            shadowColor: 'rgba(0, 0, 0, 0.5)'
                        }
                    }
                }]
            }
        }
    }
}
</script>
```