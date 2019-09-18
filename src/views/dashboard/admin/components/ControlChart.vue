<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import resize from './mixins/resize'

export default {
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    width: {
      type: String,
      default: '100%'
    },
    height: {
      type: String,
      default: '350px'
    },
    autoResize: {
      type: Boolean,
      default: true
    },
    chartData: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      chart: null
    }
  },
  watch: {
    chartData: {
      deep: true,
      handler(val) {
        this.setOptions(val)
      }
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.initChart()
    })
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    this.chart.dispose()
    this.chart = null
  },
  methods: {
    initChart() {
      this.chart = echarts.init(this.$el, 'macarons')
      this.setOptions(this.chartData)
    },
    setOptions({ target, actual, time } = {}) {
      this.chart.setOption({
        xAxis: {
          data: time,
          boundaryGap: false
        },
        title: {
          left: 'center',
          text: 'Control chart'
        },
        grid: {
          left: 10,
          right: 10,
          bottom: 20,
          top: 30,
          containLabel: true
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross'
          },
          padding: [5, 10]
        },
        yAxis: {
          name: 'pcs.',
          type: 'value'
        },
        legend: {
          data: ['target', 'actual'],
          x: 'right'
        },
        dataZoom: {
          show: true,
          realtime: true,
          start: 50,
          end: 100
        },
        series: [
          {
            name: 'target',
            smooth: true,
            type: 'line',
            itemStyle: {
              normal: {
                color: '#36a3f7',
                lineStyle: {
                  color: '#36a3f7',
                  width: 2
                }
              }
            },
            data: target,
            animationDuration: 2800,
            animationEasing: 'cubicInOut'
          },
          {
            name: 'actual',
            smooth: true,
            type: 'line',
            itemStyle: {
              normal: {
                color: '#2cb457',
                lineStyle: {
                  color: '#2cb457',
                  width: 2
                }
              }
            },
            data: actual,
            animationDuration: 2800,
            animationEasing: 'quadraticOut'
          }
        ]
      })
    }
  }
}
</script>
