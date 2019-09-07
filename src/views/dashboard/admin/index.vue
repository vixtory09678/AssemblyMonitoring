<template>
  <div class="dashboard-editor-container">
    <!-- <github-corner class="github-corner" /> -->
    <!-- <NotiErrorGroup /> -->
    <Control :data="objControlChart" />
    <OEE :data="objControlChart" />
    <ParetoChart :data="objParetoChart" />
  </div>
</template>

<script>
// import PanelGroup from './components/PanelGroup'

import Control from './components/Control'
import OEE from './components/OEE'
import ParetoChart from './components/ParetoChart'
import { SnotifyPosition } from 'vue-snotify'

export default {
  name: 'DashboardAdmin',
  components: {
    Control,
    OEE,
    ParetoChart
  },
  data() {
    return {
      // lineChartData: lineChartData.newVisitis,
      objParetoChart: [{}],
      objControlChart: {
        good: 0,
        bad: 0,
        total: 0,
        timeError: 0,
        totalTime: 0
      },
      toastError: [13],
      timerToast: [13]
    }
  },
  mqtt: {
    'data/maintenance/pareto_chart'(data) {
      this.objParetoChart = JSON.parse(data)
    },
    'data/maintenance/control_chart'(data) {
      this.objControlChart = JSON.parse(data)
    },
    'data/maintenance/alarm'(data) {
      var obj = JSON.parse(data)
      this.showAlarm(obj)
    }
  },

  methods: {
    displayNotification(html, config = {}) {
      return this.$snotify.html(html, config)
    },
    getFormNotify(title, body) {
      const html = `
      <div class="snotifyToast__title">
        <b>${title}</b>
      </div>
      <div class="snotifyToast__body">
        ${body}
      </div>`
      return html
    },
    showAlarm(object) {
      if (object.description_error === 'clear') {
        if (this.toastError[object.id_error] != null) {
          this.removeNotification(this.toastError[object.id_error].id)
          clearInterval(this.timerToast[object.id_error])
        }
      } else {
        const html = this.getFormNotify(
          object.description_error.toUpperCase() + ' ERROR!!',
          `M/C: ${object.mc_id}<br>
          Error station: ${object.id_error + 1}`
        )

        this.toastError[object.id_error] = this.displayNotification(html, {
          timeout: 0,
          type: 'warning',
          closeOnClick: false,
          position: SnotifyPosition.rightTop
        })

        this.timerToast[object.id_error] = setInterval(() => {
          this.toastError[object.id_error].config.type = 'error'
          clearInterval(this.timerToast[object.id_error])
        }, 15000)
      }
    },
    removeNotification(id) {
      this.$snotify.remove(id)
    }
  }
}
</script>

<style lang="scss">
.dashboard-editor-container {
  padding: 32px;
  background-color: rgb(240, 242, 245);
  position: relative;

  .github-corner {
    position: absolute;
    top: 0px;
    border: 0;
    right: 0;
  }

  .chart-wrapper {
    background: #fff;
    padding: 16px 16px 0;
    margin-bottom: 32px;
  }
}

@media (max-width: 1024px) {
  .chart-wrapper {
    padding: 8px;
  }
}
</style>
