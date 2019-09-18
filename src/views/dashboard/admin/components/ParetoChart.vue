<template>
  <el-row>
    <div class="chart-wrapper">
      <VueAnyChart ref="paretoChart" :options="paretoOptions" />
    </div>
  </el-row>
</template>

<script>
import Anychart from 'anychart'
import * as data from '../components/data/data'
import VueAnyChart from '../../../../components/Charts/AnyChart'
import * as error from '../components/data/error_pareto'

export default {
  name: 'ParetoChart',
  components: {
    VueAnyChart
  },
  props: {
    data: {
      type: Object,
      default: {}
    },
    dataArray: {
      type: Object,
      default: []
    }
  },
  data() {
    return {
      Anychart: Anychart,
      paretoOptions: data.ParetoData,
      objParetoChart: {
        errors: error.Error
      }
    }
  },
  watch: {
    data: {
      handler(vals) {
        if (vals === null) {
          return
        }
        if (vals === undefined) {
          return
        }

        console.log(`pareto are ${vals}`)

        this.objParetoChart.errors[vals.error.error_id].error_stack =
          vals.error.error_stack

        this.setParetoData()
      }
    },
    dataArray: {
      handler(vals) {
        if (vals === null) {
          return
        }
        if (vals === undefined) {
          return
        }

        vals.forEach(val => {
          this.objParetoChart.errors[val.error.error_id].error_stack =
            val.error.error_stack
        })

        this.setParetoData()
      }
    }
  },
  methods: {
    setParetoData() {
      var temp = []
      this.objParetoChart.errors.forEach(err => {
        temp.push({
          x: err.description,
          value: err.error_stack
        })
      })

      this.$refs.paretoChart.chart.data(temp)
    }
  }
}
</script>

<style lang="scss" scoped>
</style>
