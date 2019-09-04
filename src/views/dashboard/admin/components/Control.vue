<template>
  <div>
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
  </div>
</template>

<script>
import DataBlock from "./DataBlock";
import moment from "moment";
import ControlChart from "./ControlChart";

export default {
  name: "Control",
  props: {
    data: Object
  },
  watch: {
    data: {
      handler(val) {
        if (val === null) {
          return;
        }
        if (val === undefined) {
          return;
        }
        this.objControlChart = val;
        this.controlChart.time.push(moment().format("YYYY-MM-DD HH:mm"));
        this.controlChart.target.push(this.getTarget());
        this.controlChart.actual.push(this.getTotal());
        this.getControlChartLimit(200);
      }
    }
  },
  components: {
    ControlChart,
    DataBlock
  },
  data() {
    return {
      controlChart: {
        target: [],
        actual: [],
        time: []
      },
      objControlChart: {
        good: 0,
        bad: 0,
        total: 0,
        timeError: 0,
        totalTime: 0
      },
      targetDiv: 4
    };
  },
  methods: {
    getTarget() {
      return this.getTotalTime() / this.targetDiv;
    },
    getTotalTime() {
      return this.objControlChart.totalTime;
    },
    setTarget(percent) {
      if (percent == 0) return;
      this.targetDiv = 3600 / ((percent / 100) * 900);
    },
    getControlChartLimit(limit) {
      if (this.controlChart.time.length > limit) {
        this.controlChart.time.shift();
        this.controlChart.target.shift();
        this.controlChart.actual.shift();
      }
    },
    getTotal() {
      return this.objControlChart.total;
    }
  }
};
</script>

<style lang="scss" scoped>
</style>
