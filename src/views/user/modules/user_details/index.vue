<template>
  <div class="user-detail-wrapper">
    <el-form class="user-form" :model="data.userData" :inline="true" label-width="60px">
      <el-form-item label="用户名">
        <el-input v-model="data.userData.username" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="姓名">
        <el-input v-model="data.userData.name" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="部门">
        <el-input v-model="data.userData.department" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="团队">
        <el-input v-model="data.userData.group" :disabled="true"></el-input>
      </el-form-item>
    </el-form>
    <div class="user-record-wrapper">
      <el-table ref="recordTable" :data="currentRecordList(currentPage, pageSize)" :height="520" border style="width: 100%">
        <el-table-column label="操作时间" prop="op_time" sortable></el-table-column>
        <el-table-column label="操作人" prop="username"></el-table-column>
        <el-table-column label="操作类型" prop="op_flag" :formatter="handleFlag"></el-table-column>
        <el-table-column label="状态" prop="op_state" :formatter="handleState"></el-table-column>
        <el-table-column label="操作详情" prop="op_remark">
          <template #default={row}>
            <el-button type="text" @click="openRemark(row)">查看详情</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        :total="total"
        :current-page="currentPage"
        :page-size="pageSize"
        :page-sizes="[7, 14, 21, 28]"
        @current-change="handleCurrentChange"
        @size-change="handleSizeChange"
        layout="total, sizes, prev, pager, next, jumper">
      </el-pagination>
    </div>
  </div>
</template>

<script>
import { opFlags } from '../../../../scripts/enumClass.js'
import Axios from 'axios'
import { ElMessageBox } from 'element-ui'

export default {
  data() {
    return {
      data: {
        userData: {
          username: this.$route.params.username,
          name: this.$route.params.name,
          department: this.$route.params.department,
          group: this.$route.params.group
        }
      },
      total: 0,
      recordList: [],
      currentPage: 1,
      pageSize: 7
    }
  },
  beforeMount: async function() {
    const res = await Axios.post('/api/record/getRecordList', {username: this.$route.params.username})
    if (res.status === 200) {
      this.recordList = res.data.recordList
      this.total = this.recordList.length
    }
  },
  methods: {
    handleSizeChange: function(val) {
      this.pageSize = val
      this.currentPage = 1
    },
    handleCurrentChange: function(val) {
      this.currentPage = val
    },
    currentRecordList: function(currentPage, pageSize) {
      return this.recordList.slice((currentPage - 1) * pageSize, currentPage * pageSize)
    },
    handleFlag: function(row) {
      for (const flag in opFlags) {
        if (opFlags[flag].code === row.op_flag) {
          return opFlags[flag].msg
        }
      }
    },
    handleState: function(row) {
      return row.op_state ? '成功' : '失败'
    },
    openRemark: function(row) {
      ElMessageBox(Buffer.from(row.op_remark.data).toString('utf-8'))
    }
  }
}
</script>

<style lang="scss">
.user-detail-wrapper {
  .user-form {
    .el-form-item {
      width: 23%;
    }
    .el-input {
      width: 100%;
    }
  }
  .user-record-wrapper {
    .el-pagination {
      margin-top: 15px;
      text-align: center;
    }
  }
}
.messageBox {
  font-family: Arial, Helvetica, sans-serif;
}
</style>
