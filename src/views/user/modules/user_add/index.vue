<template>
  <div class="user-add-wrapper">
    <el-form class="user-form" :model="data.userData" :rules="rules" ref="form" label-width="120px">
      <el-form-item label="用户名" prop="username">
        <el-input v-model="data.userData.username" placeholder="请填写统一认证号"></el-input>
      </el-form-item>
      <el-form-item label="姓名" prop="name">
        <el-input v-model="data.userData.name" placeholder="请填写姓名"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input type="password" v-model="data.userData.password" placeholder="请填写密码"></el-input>
      </el-form-item>
      <el-form-item label="确认密码" prop="check">
        <el-input type="password" v-model="check" placeholder="请再次输入密码"></el-input>
      </el-form-item>
      <el-form-item label="部门" prop="department">
        <el-select v-model="data.userData.department" placeholder="请选择部门">
          <el-option v-for="item in departmentList" :key="item.depId" :label="item.name" :value="item.name"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="团队" prop="group">
        <el-select v-model="data.userData.group" placeholder="请选择团队" :popper-append-to-body="false">
          <el-option v-for="item in groupList" :key="item.groupId" :label="item.name" :value="item.name"></el-option>
        </el-select>
      </el-form-item>
      <!-- TODO [1.0.3] 修复el-select下拉框宽度过大的问题 -->
      <el-form-item class="user-add-button">
        <el-button type="primary" class="confirm" @click="submit">提交</el-button>
        <el-button type="info" class="reset" @click="reset">重置</el-button>
        <el-button class="cancel" @click="cancel">取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import { useNotification } from '../../../../scripts/notification'
import { opFlags } from '../../../../scripts/enumClass'

export default {
  data() {
    return {
      departmentList: [{depId: '1001', name: 'xxx实验室'}, {depId: '1002', name: '技术部'}],
      groupList: [{groupId: '2101', name: '分布式开发团队'}, {groupId: '2102', name: '服务支持团队'}],
      data: {
        userData: {}
      },
      check: '',
      rules: {
        password: [
          { required: true, trigger: 'blur', message: '请输入密码' }
        ],
        check: [
          { validator: this.validatePass, required: true, trigger: 'blur' }
        ],
        username: [
          { required: true, trigger: 'blur', message: '请输入统一认证号' },
          { min: 9, max: 9, trigger: 'blur', message: '统一认证号为9位数字, 请检查' }
        ],
        name: [
          { required: true, trigger: 'blur', message: '请输入姓名' }
        ],
        department: [
          { required: true, trigger: 'blur', message: '请选择部门'}
        ],
        group: [
          { required: true, trigger: 'blur', message: '请选择团队'}
        ]
      }
    }
  },
  methods: {
    validatePass: function(rule, value, callback) {
      if (!this.check) {
        callback(new Error('请再次输入密码'))
      } else if (this.check !== this.data.userData.password) {
        callback(new Error('两次输入密码不一致'))
      } else {
        callback()
      }
    },
    submit: function() {
      this.$refs.form.validate(async (valid) => {
        if (valid) {
          await this.$store.dispatch('user/addUser', {userInfo: this.data.userData}).then((res) => {
            if (res && res.status === 200) {
              useNotification().successNotification('成功', '添加用户数据成功')
              this.$store.dispatch('user/addUserRecord', {
                flag: opFlags.USER_ADD.code,
                state: true,
                remark: opFlags.USER_ADD.msg + this.data.userData.username
              })
              this.data.userData = {}
              this.check.value = ''
              this.$store.dispatch('user/getUserList')
              this.$store.commit('setOpCardShow', {flag: false})
            } else if (res.status === 202) {
              const msg = '用户名 ' + Object.values(res.data.error)[0] + ' 已存在'
              this.$store.dispatch('user/addUserRecord', {
                flag: opFlags.USER_ADD.code,
                state: false,
                remark: msg
              })
              useNotification().failNotification('失败', msg)
            }
          }).catch((e) => {
            console.log('新增用户失败: ', e)
            this.$store.dispatch('user/addUserRecord', {
              flag: opFlags.USER_ADD.code,
              state: false,
              remark: '用户新增失败: ' + e.toString()
            })
          })
        }
      })
    },
    reset: function() {
      this.data.userData = {}
      this.check = ''
    },
    cancel: function() {
      this.data.userData = {}
      this.check = ''
      this.$router.go(-1)
    }
  }
}
</script>

<style lang="scss">
.user-add-wrapper {
  .user-form {
    .el-form-item {
      width: 60%;
    }
    .el-select {
      width: 100%;
    }
    .el-input {
      width: 60%;
    }
  }
  .user-add-button {
    margin-top: 15px;
    .cancel, .reset, .confirm {
      margin: 0 10px 0px 10px;
      height: 32px;
      font-size: 14px;
      padding: 0 15px;
    }
  }
}
</style>
