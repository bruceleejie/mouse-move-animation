<script setup lang="ts">
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup
import { reactive } from 'vue';
import mouseMoveRow from './components/mouseMoveRow.vue';
// import HelloWorld from './components/HelloWorld.vue'
let list = reactive([
  {title: '深圳XXX-XXXX-XXXXX-XXXX股份有限公司、深圳YYYYY-YYYYY-ZZZZ信息技术有限公司等20家公司的域名查询', id: 1, status: '查询中', showCancel: false },
  {title: '北京SSSS信息技术有限公司、YYYYYYttttt事务所等5家公司的专利查询', id: 1, status: '查询中', showCancel: false },
  {title: 'AAAA市XXXXX区YYYY街道ZZZZZ小区17号楼6单元901室，天气有点阴', id: 1, status: '查询中', showCancel: false },
  {title: 'BBBBB省CCCCC市DDDDDD县EEEE中学FFFFFF班，天气还可以，阳光明媚！', id: 1, status: '查询超时', showCancel: false },
  {title: 'hello world，你好，这里是北京，马上五一假期了，很开心啊，巴拉巴拉的一大堆', id: 1, status: '查询超时', showCancel: false },
  {title: '这里文字很短应该不需要hover移动', id: 1, status: '查询超时', showCancel: true },
]);
// hover显示取消任务行
function handleShowCancel(obj:any, index:any) {
  setTimeout(() => {
    list[index].showCancel = true;
  })
}
function handleHideCancel(obj:any, index:any) {
  setTimeout(() => {
    list[index].showCancel = false;
  });
}
function tableRowClickBack(options:any) {
  console.log(27, options)
  // 测试sourcetree
}
</script>

<template>
  <div class="hover-container">
    <div class="box"
      v-for="(item, index) in list"
      :key="item.id"
      @mouseenter="handleShowCancel(item, index)"
      @mouseleave="handleHideCancel(item, index)"
    >
      <template v-if="!item['showCancel']">
        <div class="left-info">{{ item.title }}</div>
        <div :class="['right-status', item.status=='查询超时'?'over':'']">{{ item.status }}</div>
      </template>
      <mouseMoveRow v-show="item['showCancel']" :rowData="item" @tableRowClickBack="tableRowClickBack" />
    </div>
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
<style scoped lang="scss">
.hover-container {
  width: 486px;
  height: 382px;
  padding: 32px;
  border-radius: 16px;
  border: 2px solid var(--Gray-200, #E4E7EC);
  background: var(--Gray-25, #FCFCFD);
  box-shadow: 0px 4px 15px 0px rgba(44, 44, 44, 0.05);
  .box {
    // display: inline-flex;
    display: flex;
    padding: 8px 0;
    border-bottom: 1px solid var(--Gray-200, #E4E7EC);
    .left-info {
      font-size: 14px;
      color: #303133;
      flex-shrink: 1;
      flex-grow: 1;
      white-space:nowrap;
      overflow:hidden;
      text-overflow:ellipsis;
      text-align: left;
    }
    .right-status {
      flex-shrink: 0;
      flex-grow: 0;
      width: 85px;
      font-size: 14px;
      color: #606266;
      text-align: left;
      &.over {
        color: #F04438;
      }
    }
  }
}
</style>
