<template>
  <div class="dashboard-editor-container">
    <!-- <github-corner class="github-corner" /> -->
    <el-row :gutter="8">
      <el-col :xs="24" :sm="24" :lg="18">
        <div class="chart-wrapper">
          <ControlChart :chart-data="controlChart" />
        </div>
      </el-col>
      <el-col :xs="24" :sm="24" :lg="6">
        <el-row>
          <data-block
            :data="getTotal()"
            :descriptions="'Actual'"
            :color="'card-panel-icon-wrapper green'"
            :type="'count'"
          />
        </el-row>
        <el-row>
          <data-block
            :data="getTarget()"
            :descriptions="'Target'"
            :color="'card-panel-icon-wrapper blue'"
            :type="'count'"
          />
        </el-row>
        <el-row>
          <data-block
            :data="getTotal() - getTarget()"
            :descriptions="'Diff'"
            :color="'card-panel-icon-wrapper red'"
            :type="'diff'"
          />
        </el-row>
      </el-col>
    </el-row>

    <!-- <machine-status :good="getGood()" :bad="getBad()" :total="getTotal()" /> -->
    <OEEBlockPanel
      :availability="getAvailability() * 100"
      :performance="getPerformance() * 100"
      :quality="getQuality() * 100"
      :oee="getOEE() * 100"
    />

    <el-row>
      <div class="chart-wrapper">
        <VueAnyChart :options="paretoOptions" ref="paretoChart" />
      </div>
    </el-row>
  </div>
</template>

<script>
// import PanelGroup from './components/PanelGroup'
import ControlChart from "./components/ControlChart";
import OEEBlockPanel from "./components/OEEBlockPanel";
import DataBlock from "./components/DataBlock";
import VueAnyChart from "../../../components/Charts/AnyChart";

import * as data from "./components/data/data";
import moment from "moment";
import Anychart from "anychart";

export default {
  name: "DashboardAdmin",
  components: {
    // PanelGroup,
    VueAnyChart,
    ControlChart,
    OEEBlockPanel,
    DataBlock
  },
  data() {
    return {
      // lineChartData: lineChartData.newVisitis,
      Anychart: Anychart,
      paretoOptions: data.ParetoData,
      objControlChart: {
        good: 0,
        bad: 0,
        total: 0,
        timeError: 0,
        totalTime: 0
      },
      objParetoChart: {
        errors: [{}]
      },
      controlChart: {
        target: [],
        actual: [],
        time: []
      },
      targetDiv: 4
    };
  },
  methods: {
    setTarget(percent) {
      if (percent == 0) return;
      this.targetDiv = 3600 / ((percent / 100) * 900);
    },
    getTimeError() {
      return this.objControlChart.timeError;
    },
    getTotal() {
      return this.objControlChart.total;
    },
    getTotalTime() {
      return this.objControlChart.totalTime;
    },
    getTarget() {
      return this.getTotalTime() / this.targetDiv;
    },
    getGood() {
      return this.objControlChart.good;
    },
    getBad() {
      return this.objControlChart.bad;
    },
    getControlChartLimit(limit) {
      if (this.controlChart.time.length > limit) {
        this.controlChart.time.shift();
        this.controlChart.target.shift();
        this.controlChart.actual.shift();
      }
    },
    getAvailability() {
      return (this.getTotalTime() - this.getTimeError()) / this.getTotalTime();
    },
    getPerformance() {
      return this.getTotal() / this.getTarget();
    },
    getQuality() {
      return this.getGood() / this.getTotal();
    },
    getOEE() {
      return this.getQuality() * this.getPerformance() * this.getAvailability();
    },
    setParetoData() {
      var i;
      var temp = [];
      this.objParetoChart.errors.forEach(err => {
        temp.push({
          x: err.description,
          value: err.error_stack
        });
      });

      this.$refs.paretoChart.chart.data(temp);
    }
  },
  mqtt: {
    "data/maintenance/control_chart"(data) {
      this.objControlChart = JSON.parse(data);
      // var date = new Date();
      // console.log(moment().format("YYYY-MM-DD HH:mm"));
      this.controlChart.time.push(moment().format("YYYY-MM-DD HH:mm"));
      this.controlChart.target.push(this.getTarget());
      this.controlChart.actual.push(this.getTotal());
      this.getControlChartLimit(200);
    },
    "data/maintenance/pareto_chart"(data) {
      console.log(`data is ${data}`);
      // console.log(`raw data is ${data}`);
      // console.log(`data is ${this.objParetoChart.errors[0].description}`);
      this.objParetoChart = JSON.parse(data);
      this.setParetoData();
    }
  }
};
</script>

<style lang="scss" scoped>
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
