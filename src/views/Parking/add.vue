<template>
  <div class="parking-add">
    <el-form ref="form" :rules="rules" :model="form_data" label-width="120px">
      <el-form-item label="停车场名称" prop="parkingName">
        <el-input v-model="form_data.parkingName"></el-input>
      </el-form-item>
      <el-form-item label="区域">
        <CityArea
          :mapLocation="true"
          :cityAreaValue.sync="form_data.area"
          @callback="callbackComponent"
        />
      </el-form-item>
      <el-form-item label="类型">
        <el-radio-group v-model="form_data.type">
          <el-radio label="1">室内</el-radio>
          <el-radio label="2">室外</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="可停放车辆">
        <el-input v-model="form_data.carsNumber"></el-input>
      </el-form-item>
      <el-form-item label="禁启用">
        <el-radio-group v-model="form_data.status">
          <el-radio label="1">禁用</el-radio>
          <el-radio label="2">启用</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="位置">
        <div class="address-map">
          <AMap @callback="callbackComponent" ref="amap" />
        </div>
      </el-form-item>
      <el-form-item label="经纬度">
        <el-input v-model="form_data.lnglat"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button :loading="button_loading" type="danger" @click="onSubmit"
          >确定</el-button
        >
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
// AMAP
import AMap from "../amap";
// 组件
import CityArea from "@c/common/cityArea";
import { ParkingAdd, ParkingDetailed, ParkingEdit } from "@/api/parking";
export default {
  name: "ParkingAdd",
  data() {
    return {
      // 表单数据配置
      form_data: {
        parkingName: "",
        area: "",
        address: "",
        type: "",
        carsNumber: "",
        status: "",
        lnglat: "",
      },
      rules: {
        parkingName: [
          { required: true, message: "请输入停车场名称", trigger: "change" },
        ],
      },
      // 按钮加载
      button_loading: false,
    };
  },
  components: { AMap, CityArea },
  mounted() {},
  methods: {
    callbackComponent(params) {
      if (params.function) {
        this[params.function](params.data);
      }
    },
    setMapCenter(data) {
      this.$refs.amap.setMapCenter(data.address);
    },
    onSubmit() {
      console.log("submit!");
      this.addParking();
    },
    /** 获取经纬度 */
    getLnglat(data) {
      // console.log(data);
      this.form_data.lnglat = data.lnglat.value;
    },
    /** 重置表单 */
    reset(formName) {
      this.$refs[formName].resetFields();
      // 清除 cityAray 的值
      this.$refs.cityArea.clear();
      // 清除地图覆盖物
      this.$refs.amap.clearMarker();
    },
    /** 新增停车场API */
    addParking() {
      this.button_loading = true;
      ParkingAdd(this.form_data)
        .then((response) => {
          this.$message({
            type: "primary",
            message: response.message,
          });
          this.button_loading = false;
          this.reset("form");
        })
        .catch((error) => {
          this.button_loading = false;
        });
    },
  },
};
</script>
<style lass="scss" scoped>
.address-map {
  width: 100%;
  height: 500px;
}
</style>