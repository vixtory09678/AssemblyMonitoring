<template>
  <div class="block">
    <el-form ref="form" :model="form" label-width="120px">
      <el-form-item label="Machine ID:">
        <el-select v-model="form.mc_id" placeholder="please select your M/C ID">
          <div v-for="item in mc_list" :key="item.id">
            <el-option :label="`M/C: ${item}`" :value="item" />
          </div>
        </el-select>
      </el-form-item>
      <el-form-item label="Select date">
        <el-col :span="24">
          <el-date-picker
            v-model="form.date"
            value-format="yyyy-MM-dd"
            placeholder="Date picker"
            type="date"
          />
        </el-col>
      </el-form-item>

      <el-form-item label="Slect data type">
        <el-radio-group v-model="form.type">
          <el-radio label="control">Control chart</el-radio>
          <el-radio label="oee">OEE, ...</el-radio>
          <el-radio label="error">Errors</el-radio>
        </el-radio-group>
      </el-form-item>

      <el-form-item>
        <el-button type="success" @click="onSubmit">Process</el-button>
      </el-form-item>
    </el-form>

    <el-dialog :visible.sync="dialogVisible" title="Error" width="30%" center>
      <span>{{ message }}</span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">Cancel</el-button>
        <el-button type="primary" @click="dialogVisible = false">Confirm</el-button>
      </span>
    </el-dialog>

    <div v-if="form.type == 'control'">
      <ControlChartSum :chart-data="controlChart" class="chart-wrapper" />
    </div>
    <div v-else-if="form.type == 'oee'">
      <div class="chart-wrapper">
        <p>OEE Comming soon</p>
      </div>
    </div>
    <div v-else>
      <div class="chart-wrapper">
        <p>Error Comming soon</p>
      </div>
    </div>
  </div>
</template>

<script>
import ControlChartSum from './components/ControlChartSum'
import moment from 'moment'
export default {
  components: {
    ControlChartSum
  },
  data() {
    return {
      info: null,
      mc_list: ['assy1', 'assy2', 'assy3'],

      form: {
        mc_id: '',
        date: '',
        type: 'control'
      },
      dialogVisible: false,
      message: '',
      data: [],
      controlChart: {
        target: [],
        actual: [],
        time: []
      },
      targetDiv: 4
    }
  },
  methods: {
    renderGraph(self) {
      self.data.forEach(obj => {
        var date = moment(obj.Timestamp).format('HH:mm')
        const target = obj.data.totalTime / self.targetDiv
        this.controlChart.time.push(date)
        this.controlChart.target.push(target)
        this.controlChart.actual.push(obj.data.total)
      })
    },
    onSubmit() {
      if (this.form.mc_id.length === 0 || this.form.date.length === 0) {
        this.message = 'Machine ID or Date is not select'
        this.dialogVisible = true
        return
      }

      const jsonData = {
        name: this.form.mc_id,
        data_type: this.form.type,
        date: this.form.date
      }
      var self = this
      this.axios
        .post(
          'http://localhost:3000/machine_data/' + self.form.type,
          jsonData,
          {
            headers: {}
          }
        )
        .then(function(response) {
          self.data = response.data.slice()
          self.controlChart.target = []
          self.controlChart.actual = []
          self.controlChart.time = []
          console.log(self.data)

          self.renderGraph(self)
        })
        .catch(function(error) {
          console.log(`${error}`)
        })
    }
  }
}
</script>

<style lang="scss" scoped>
.block {
  padding: 20px;
}
.chart-wrapper {
  padding-left: 30px;
  padding-right: 30px;
}
</style>
