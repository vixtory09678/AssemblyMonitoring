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

    <ControlChartSum :chart-data="controlChart" class="chart-wrapper" />
    <GroupBlockControl
      class="block"
      :actual="updateControlBlock.actual"
      :target="updateControlBlock.target"
      :good="updateControlBlock.good"
      :bad="updateControlBlock.bad"
    />

    <GroupBlockOEE
      class="block"
      :oee="updateOEEBlock.oee"
      :availability="updateOEEBlock.ava"
      :performance="updateOEEBlock.per"
      :quality="updateOEEBlock.qua"
    />
  </div>
</template>

<script>
import ControlChartSum from "./components/ControlChartSum";
import GroupBlockControl from "./components/GroupBlockControl";
import GroupBlockOEE from "./components/GroupBlockOEE";
import moment from "moment";
export default {
  components: {
    ControlChartSum,
    GroupBlockControl,
    GroupBlockOEE
  },
  data() {
    return {
      info: null,
      mc_list: ["assy1", "assy2", "assy3"],

      form: {
        mc_id: "",
        date: "",
        type: "control"
      },
      dialogVisible: false,
      message: "",
      data: [],
      controlChart: {
        target: [],
        actual: [],
        time: []
      },

      updateControlBlock: {
        actual: 0,
        target: 0,
        good: 0,
        bad: 0
      },

      updateOEEBlock: {
        oee: 0,
        ava: 0,
        per: 0,
        qua: 0
      },

      targetDiv: 4
    };
  },
  methods: {
    renderGraph(self) {
      self.data.forEach(obj => {
        var date = moment(obj.Timestamp).format("HH:mm");
        const target = obj.data.totalTime / self.targetDiv;
        const mcRun = obj.data.totalTime - obj.data.timeError;
        this.controlChart.time.push(date);
        this.controlChart.target.push(target);
        this.controlChart.actual.push(obj.data.total);

        this.updateControlBlock.actual = obj.data.total;
        this.updateControlBlock.target = target;
        this.updateControlBlock.good = obj.data.good;
        this.updateControlBlock.bad = obj.data.bad;

        this.updateOEEBlock.ava = mcRun / obj.data.totalTime;
        this.updateOEEBlock.per = obj.data.total / target;
        this.updateOEEBlock.qua = obj.data.good / obj.data.total;
        this.updateOEEBlock.oee =
          this.updateOEEBlock.ava *
          this.updateOEEBlock.per *
          this.updateOEEBlock.qua;
      });
    },
    onSubmit() {
      if (this.form.mc_id.length === 0 || this.form.date.length === 0) {
        this.message = "Machine ID or Date is not select";
        this.dialogVisible = true;
        return;
      }

      const jsonData = {
        name: this.form.mc_id,
        data_type: this.form.type,
        date: this.form.date
      };

      console.log(jsonData);

      var self = this;
      this.axios
        .post(
          "http://54.179.167.18:3000/machine_data/" + self.form.type,
          jsonData,
          {
            headers: {}
          }
        )
        .then(function(response) {
          self.data = response.data.slice();
          self.controlChart.target = [];
          self.controlChart.actual = [];
          self.controlChart.time = [];
          console.log(self.data);

          self.renderGraph(self);
        })
        .catch(function(error) {
          console.log(`${error}`);
        });
    }
  }
};
</script>

<style lang="scss" scoped>
.block {
  padding: 20px;
}
.chart-wrapper {
  padding-left: 30px;
  padding-right: 30px;
}
.icon-size {
  font-size: 2rem;
}
</style>
