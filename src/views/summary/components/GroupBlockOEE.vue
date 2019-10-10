<template>
  <el-row :gutter="40" class="panel-group">
    <el-col :xs="12" :sm="12" :lg="6" class="card-panel-col">
      <div class="card-panel">
        <div class="card-panel-icon-wrapper icon-actual">
          <svg-icon icon-class="cogwheel" class-name="card-panel-icon" />
        </div>
        <div class="card-panel-description">
          <div class="card-panel-text">OEE</div>
          <count-to
            :start-val="0"
            :end-val="(oee*100)"
            :duration="2600"
            :suffix="' %'"
            class="card-panel-num"
          />
        </div>
      </div>
    </el-col>
    <el-col :xs="12" :sm="12" :lg="6" class="card-panel-col">
      <div class="card-panel">
        <div class="card-panel-icon-wrapper icon-target">
          <svg-icon icon-class="hours" class-name="card-panel-icon" />
        </div>
        <div class="card-panel-description">
          <div class="card-panel-text">Availability</div>
          <count-to
            :start-val="0"
            :end-val="(availability*100)"
            :duration="2600"
            :suffix="' %'"
            class="card-panel-num"
          />
        </div>
      </div>
    </el-col>
    <el-col :xs="12" :sm="12" :lg="6" class="card-panel-col">
      <div class="card-panel">
        <div class="card-panel-icon-wrapper icon-good">
          <svg-icon icon-class="mass-production" class-name="card-panel-icon" />
        </div>
        <div class="card-panel-description">
          <div class="card-panel-text">Performance</div>
          <count-to
            :start-val="0"
            :end-val="(performance * 100)"
            :duration="2600"
            :suffix="' %'"
            class="card-panel-num"
          />
        </div>
      </div>
    </el-col>
    <el-col :xs="12" :sm="12" :lg="6" class="card-panel-col">
      <div class="card-panel">
        <div class="card-panel-icon-wrapper icon-bad">
          <svg-icon icon-class="settings" class-name="card-panel-icon" />
        </div>
        <div class="card-panel-description">
          <div class="card-panel-text">Quality</div>
          <count-to
            :start-val="0"
            :end-val="(quality * 100)"
            :duration="2600"
            :suffix="' %'"
            class="card-panel-num"
          />
        </div>
      </div>
    </el-col>
  </el-row>
</template>

<script>
import CountTo from "vue-count-to";
export default {
  name: "GroupBlockOEE",
  components: {
    CountTo
  },
  props: {
    oee: 0,
    availability: 0,
    performance: 0,
    quality: 0
  },
  methods: {
    handleSetLineChartData(type) {
      this.$emit("handleSetLineChartData", type);
    }
  }
};
</script>

<style lang="scss" scoped>
.panel-group {
  margin-top: 18px;
  .card-panel-col {
    margin-bottom: 32px;
  }
  .card-panel {
    height: 108px;
    cursor: pointer;
    font-size: 12px;
    position: relative;
    overflow: hidden;
    color: #666;
    background: #fff;
    box-shadow: 4px 4px 40px rgba(0, 0, 0, 0.05);
    border-color: rgba(0, 0, 0, 0.05);
    &:hover {
      .card-panel-icon-wrapper {
        color: #fff;
      }
      .icon-actual {
        background: #40c9c6;
      }
      .icon-target {
        background: #36a3f7;
      }
      .icon-bad {
        background: #f4516c;
      }
      .icon-good {
        background: #34bfa3;
      }
    }
    .icon-actual {
      color: #40c9c6;
    }
    .icon-target {
      color: #36a3f7;
    }
    .icon-bad {
      color: #f4516c;
    }
    .icon-good {
      color: #34bfa3;
    }
    .card-panel-icon-wrapper {
      float: left;
      margin: 14px 0 0 14px;
      padding: 16px;
      transition: all 0.38s ease-out;
      border-radius: 6px;
    }
    .card-panel-icon {
      float: left;
      font-size: 48px;
    }
    .card-panel-description {
      float: right;
      font-weight: bold;
      margin: 26px;
      margin-left: 0px;
      .card-panel-text {
        line-height: 18px;
        color: rgba(0, 0, 0, 0.45);
        font-size: 16px;
        margin-bottom: 12px;
      }
      .card-panel-num {
        font-size: 20px;
      }
    }
  }
}
@media (max-width: 550px) {
  .card-panel-description {
    display: none;
  }
  .card-panel-icon-wrapper {
    float: none !important;
    width: 100%;
    height: 100%;
    margin: 0 !important;
    .svg-icon {
      display: block;
      margin: 14px auto !important;
      float: none !important;
    }
  }
  .icon-size {
    font-size: 2rem;
  }
}
</style>
