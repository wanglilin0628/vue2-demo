<template>
  <el-form ref="form" :model="formData" label-width="s80px">
    <el-form-item label="姓名" class="input-form-item">
      <el-input v-model="formData.name" placeholder="请输入姓名"></el-input>
    </el-form-item>
    <el-form-item label="性别" class="input-form-item">
      <el-radio label="1" v-model="formData.sex">男</el-radio>
      <el-radio label="0" v-model="formData.sex">女</el-radio>
    </el-form-item>
    <el-form-item label="籍贯" class="input-select-item">
      <!-- <el-cascader :options="pcaData" v-model="formData.origin"></el-cascader> -->
      <el-select v-model="formData.province" placeholder="请选择所在省份" @change="handleProvinceChange">
        <el-option v-for="(item, i) in province" :label="item.name" :value="i" :key="i"></el-option>
      </el-select>
      <el-select v-if="hasCityData" v-model="formData.city" placeholder="请选择所在市" @change="handleCityChange">
        <el-option v-for="(item, i) in city" :label="item.name" :value="i" :key="i"></el-option>
      </el-select>
      <el-select v-if="hasCountyData" v-model="formData.county" placeholder="请选择所在县">
        <el-option v-for="(item, i) in county" :label="item.name" :value="i" :key="i"></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="主题颜色" class="input-form-item">
      <el-color-picker v-model="formData.color"></el-color-picker>
    </el-form-item>
    <el-form-item label="s公司员工" class="input-form-item">
      <el-switch v-model="formData.isStaff"></el-switch>
    </el-form-item>
  </el-form>
</template>

<script>
import pca from '../../../../public/data/pca-code.json'
export default {
  data() {
    return {
      formData: {},
      province: pca.province,
      city: [],
      county: [],
      hasCityData: false,
      hasCountyData: false
    }
  },
  methods: {
    handleProvinceChange: function(value) {
      this.city = this.province[value].children
      this.formData.city = ''
      this.formData.county = ''
      if (this.city && this.city.length > 0) {
        this.hasCityData = true
      }
    },
    handleCityChange: function(value) {
      this.county = this.city[value].children
      this.formData.county = ''
      if (this.county && this.county.length > 0) {
        this.hasCountyData = true
      }
    }
  }
}
</script>

<style lang='scss'>
.input-form-item {
  padding-top: 15px;
  width: 50%;
}
.input-select-item {
  padding-top: 10px;
  width: 100%;
  .el-select {
    width: 30%;
    padding-left: 5px;
  }
}
</style>
