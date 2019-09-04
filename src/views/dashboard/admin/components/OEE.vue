<template>
  <OEEBlockPanel
    :availability="getAvailability() * 100"
    :performance="getPerformance() * 100"
    :quality="getQuality() * 100"
    :oee="getOEE() * 100"
  />
</template>

<script>
import OEEBlockPanel from "./OEEBlockPanel";

export default {
  components: {
    OEEBlockPanel
  },
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
      }
    }
  },
  data() {
    return {
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
    getTimeError() {
      return this.objControlChart.timeError;
    },
    getGood() {
      return this.objControlChart.good;
    },
    getBad() {
      return this.objControlChart.bad;
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
    getTotal() {
      return this.objControlChart.total;
    }
  }
};
</script>

<style lang="scss" scoped>
</style>
