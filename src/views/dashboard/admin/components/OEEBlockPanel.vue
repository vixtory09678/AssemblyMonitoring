<template>
  <el-row :gutter="40" class="panel-group">
    <el-col :xs="24" :sm="24" :lg="6" class="card-panel-col">
      <div class="card-panel">
        <div class="card-panel-icon-wrapper icon-quality">
          <svg-icon icon-class="excel" class-name="card-panel-icon" />
        </div>
        <div class="card-panel-description">
          <div class="card-panel-text">OEE</div>
          <count-to
            :start-val="oeeOld"
            :end-val="oee"
            :duration="3200"
            :decimals="2"
            :suffix="'%'"
            class="card-panel-num"
          />
        </div>
      </div>
    </el-col>

    <el-col :xs="24" :sm="24" :lg="6" class="card-panel-col">
      <div class="card-panel">
        <div class="card-panel-icon-wrapper icon-good">
          <svg-icon icon-class="excel" class-name="card-panel-icon" />
        </div>
        <div class="card-panel-description">
          <div class="card-panel-text">Availability</div>
          <count-to
            :start-val="availabilityOld"
            :end-val="availability"
            :duration="2600"
            :decimals="2"
            :suffix="'%'"
            class="card-panel-num"
          />
        </div>
      </div>
    </el-col>

    <el-col :xs="24" :sm="24" :lg="6" class="card-panel-col">
      <div class="card-panel">
        <div class="card-panel-icon-wrapper icon-good">
          <svg-icon icon-class="excel" class-name="card-panel-icon" />
        </div>
        <div class="card-panel-description">
          <div class="card-panel-text">Performance</div>
          <count-to
            :start-val="performanceOld"
            :end-val="performance"
            :duration="2600"
            :decimals="2"
            :suffix="'%'"
            class="card-panel-num"
          />
        </div>
      </div>
    </el-col>

    <el-col :xs="24" :sm="24" :lg="6" class="card-panel-col">
      <div class="card-panel">
        <div class="card-panel-icon-wrapper icon-bad">
          <svg-icon icon-class="excel" class-name="card-panel-icon" />
        </div>
        <div class="card-panel-description">
          <div class="card-panel-text">Quality</div>
          <count-to
            :start-val="qualityOld"
            :end-val="quality"
            :duration="3000"
            :decimals="2"
            :suffix="'%'"
            class="card-panel-num"
          />
        </div>
      </div>
    </el-col>
  </el-row>
</template>

<script>
import CountTo from 'vue-count-to'

export default {
  name: 'OEEBlockPanel',
  components: {
    CountTo
  },
  props: {
    availability: {
      type: Number,
      default: 0
    },
    performance: {
      type: Number,
      default: 0
    },
    quality: {
      type: Number,
      default: 0
    },
    oee: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      availabilityOld: 0,
      performanceOld: 0,
      qualityOld: 0,
      oeeOld: 0
    }
  },
  watch: {
    availability: function(newVal, oldVal) {
      this.availabilityOld = oldVal
    },
    performance: function(newVal, oldVal) {
      this.performanceOld = oldVal
    },
    quality: function(newVal, oldVal) {
      this.qualityOld = oldVal
    },
    oee: function(newVal, oldVal) {
      this.oeeOld = oldVal
    }
  }
}
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

      .icon-good {
        background: #40c9c6;
      }

      .icon-quality {
        background: #36a3f7;
      }

      .icon-bad {
        background: #f4516c;
      }

      .icon-shopping {
        background: #34bfa3;
      }
    }

    .icon-good {
      color: #40c9c6;
    }

    .icon-quality {
      color: #36a3f7;
    }

    .icon-bad {
      color: #f4516c;
    }

    .icon-shopping {
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
}
</style>
