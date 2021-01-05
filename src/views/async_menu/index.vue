<template>
  <div class="menu-wrapper">
    <div class="menu-container">
      <el-menu @select="selectMenu" class="menu-main" :default-active="menuData.defaultMenu" text-color="#FFFFFF" active-text-color="#DE4344" background-color="#394551">
        <el-menu-item index='home' class="menu-item">
          <template #title>首页</template>
        </el-menu-item>
        <el-submenu class="menu-submenu" v-for="(item, id) in menuData.retMenu" :key="item.id" :index="id.toString()">
          <template #title>{{item.defaultLabel}}</template>
          <el-menu-item v-for="(subItem, subId) in item.childrens" :key="subItem.id" :index="id + '_' + subId" class="menu-item">
            <template #title>{{subItem.defaultLabel}}</template>
          </el-menu-item>
        </el-submenu>
      </el-menu>
    </div>
  </div>

</template>

<script>
import data from '../../../public/data/menu.json'
import { mapMutations } from 'vuex'

export default {
  name: 'async-menu',
  data() {
    return {
      menuData: {
        retMenu: (data.result.retMenu)[0].childrens[0].childrens,
        defaultMenu: ''
      }
    }
  },
  methods: {
    selectMenu: function(index) {
      this.setOpCardShow({flag: false})
      if (index !== 'home') {
        window.sessionStorage.setItem('default-menu', index)
        this.$router.push({path: this.getUrl(index)})
      } else {
        window.sessionStorage.removeItem('default-menu')
        this.$router.push({path: '/'})
      }
    },
    getUrl: function(index) {
      const indexs = index.split('_')
      return this.menuData.retMenu[indexs[0]].childrens[indexs[1]].url
    },
    getDefaultMenu: function() {
      const tmp = window.sessionStorage.getItem('default-menu')
      return tmp !== null && tmp !== undefined ? tmp : 'home'
    },
    ...mapMutations(['setOpCardShow'])
  }
}
</script>

<style lang="scss">
  .menu-wrapper {
    .menu-container {
      display: block;
      position: absolute;
      border-radius: 3px;
      top: 64px;
      left: 0px;
      bottom: 4px;
      width: 242px;
      z-index: 2;
      overflow: auto;
      background-color: #394551;
      transition: all .3s;
      overflow: hidden;
      .menu-main {
        width: 100%!important;
        overflow: visible!important;
        .menu-submenu {
          padding-right: 0;
          width: 100%;
          .menu-item {
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
          }
        }
      }
    }
  }
</style>
