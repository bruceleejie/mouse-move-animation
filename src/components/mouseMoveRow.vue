<template>
  <div class="component component-row-item">
    <div class="left-info" ref="infoFather" >
        <div class="left-mask"></div>
        <div class="name-info" ref="nameInfo">{{props.rowData.title}}</div>
        <div class="right-mask"></div>
        <div class="mask-all"></div>
    </div>
    <div class="right-btn" @click.stop="handleCancelTask(props.rowData)">{{props.rowData.status == 1? '终止任务' : '取消任务'}}</div>
  </div>
</template>

<script lang="ts" setup>
import { onMounted, ref } from 'vue'
interface Props {
  rowData: object
}
const props = withDefaults(defineProps<Props>(), {
  rowData: ()=>{
    return {
      title: '',
      status: ''
    }
  }
})
const emits = defineEmits(['tableRowClickBack'])
let rowItem = ref<any>(null)
let infoFather = ref<any>(null)
let nameInfo = ref<any>(null)
let startPointToLeftDistance = ref(0);
let fatherEleWidth = ref(0);
let childEleWidth = ref(0);
let startPointToRightDistance = ref(0);
let childHideDistance = ref(0);
let lastOffsetX = ref(0);
let movePercent = ref('');
let childMoveDistance = ref(0);
let mouseFrom = ref('right');
let showLeftMask = ref(false);

onMounted(() => {
  infoFather.value.addEventListener('mousemove', (e:any) => {
    if(e.target.parentElement.childNodes[1].clientWidth > e.target.parentElement.clientWidth - 30) {
            // 子元素大于 右侧遮罩内的宽度才有移动交互
      if(startPointToLeftDistance.value == 0) {
          // 第一次hover进来
          fatherEleWidth.value = e.target.parentElement.clientWidth - 30;
          childEleWidth.value = e.target.parentElement.childNodes[1].clientWidth;
          // console.log(77, '第一次进', e.offsetX, fatherEleWidth.value, childEleWidth.value);
          if(fatherEleWidth.value + 30 - 2 <= e.offsetX && e.offsetX <= fatherEleWidth.value + 30) {
              mouseFrom.value = 'right';
              // console.log(79, '最右侧进入', mouseFrom.value)
              startPointToLeftDistance.value = e.offsetX;
              startPointToRightDistance.value = fatherEleWidth.value - e.offsetX;
              childHideDistance.value = childEleWidth.value - fatherEleWidth.value;
              movePercent.value = (childHideDistance.value / startPointToRightDistance.value).toFixed(2);
          } else if(e.offsetX <= 1) {
              mouseFrom.value = 'left';
              // console.log(85, '最左侧进入', mouseFrom.value)
              startPointToLeftDistance.value = e.offsetX;
              startPointToRightDistance.value = fatherEleWidth.value - e.offsetX;
              childHideDistance.value = childEleWidth.value - fatherEleWidth.value;
              movePercent.value = (childHideDistance.value / startPointToRightDistance.value).toFixed(2);
          } else {
              mouseFrom.value = 'middle';
              // console.log(91, '中间进入', mouseFrom.value)
              startPointToLeftDistance.value = e.offsetX;
              startPointToRightDistance.value = fatherEleWidth.value - e.offsetX;
              childHideDistance.value = childEleWidth.value - fatherEleWidth.value;
              movePercent.value = (childHideDistance.value / startPointToRightDistance.value).toFixed(2);
          }
          lastOffsetX.value = e.offsetX;
      } else{
        // 第2次 n次hover进来 fatherEleWidth.value - 1 <= startPointToLeftDistance.value && startPointToLeftDistance.value <= fatherEleWidth.value + 1
        if(mouseFrom.value == 'right') {
            // 最右侧来的
            // console.log(106, '右侧', e.offsetX, startPointToLeftDistance.value);
            if(lastOffsetX.value <= e.offsetX) {
                // 鼠标向右
                if(startPointToLeftDistance.value >= fatherEleWidth.value + 30 - 2) {
                    // console.log(110, '上一次的位置还是在最右边');
                    startPointToLeftDistance.value = e.offsetX;
                    startPointToRightDistance.value = fatherEleWidth.value - e.offsetX;
                    childHideDistance.value = childEleWidth.value - fatherEleWidth.value;
                    movePercent.value = (childHideDistance.value / startPointToRightDistance.value).toFixed(2);
                } else {
                    let mouseMoveDistance = e.offsetX - lastOffsetX.value;
                    let infoMoveDistance = mouseMoveDistance * Number(movePercent.value);
                    childMoveDistance.value = childMoveDistance.value + infoMoveDistance;
                    // console.log(118, lastOffsetX.value, movePercent.value, mouseMoveDistance, infoMoveDistance, childMoveDistance.value, childHideDistance.value);
                    if(childMoveDistance.value >= childHideDistance.value) {
                        e.target.parentElement.childNodes[1].style.transform = `translateX(-${childHideDistance.value}px) translateY(0px)`;
                        childMoveDistance.value = childHideDistance.value;
                    } else {
                        e.target.parentElement.childNodes[1].style.transform = `translateX(-${childMoveDistance.value}px) translateY(0px)`;
                    }
                }
            } else {
                  // 鼠标向左
                  let mouseMoveDistance = lastOffsetX.value - e.offsetX;
                  let infoMoveDistance = Math.abs(mouseMoveDistance) * Number(movePercent.value);
                  childMoveDistance.value = Math.abs(childMoveDistance.value - infoMoveDistance);
                  // console.log(132, '鼠标向左了开始', mouseMoveDistance, infoMoveDistance, childMoveDistance.value);
                  if(e.offsetX > startPointToLeftDistance.value) {
                      // 鼠标还在起始点右侧
                      e.target.parentElement.childNodes[1].style.transform = `translateX(-${childMoveDistance.value}px) translateY(0px)`;
                  } else {
                      // 鼠标在起始点或者移动到了起始点左侧
                      // console.log(137, '鼠标回到起始点');
                      e.target.parentElement.childNodes[1].style.transform = `translateX(0px)`;
                      // if(startPointToLeftDistance.value >= fatherEleWidth.value + 30 - 2)
                      startPointToLeftDistance.value = e.offsetX;
                      startPointToRightDistance.value = fatherEleWidth.value - e.offsetX;
                      childHideDistance.value = childEleWidth.value - fatherEleWidth.value;
                      movePercent.value = (childHideDistance.value / startPointToRightDistance.value).toFixed(2);
                  }
            }
                // } else if(startPointToLeftDistance.value <= 1) {
        } else {
          // console.log(149, '左侧或者中间', e.offsetX);
          if(lastOffsetX.value <= e.offsetX) {
              // 鼠标向右
              let mouseMoveDistance = e.offsetX - lastOffsetX.value;
              let infoMoveDistance = mouseMoveDistance * Number(movePercent.value);
              childMoveDistance.value = childMoveDistance.value + infoMoveDistance;
              // console.log(155, lastOffsetX.value, movePercent.value, mouseMoveDistance, infoMoveDistance, childMoveDistance.value);
              if(childMoveDistance.value >= childHideDistance.value) {
                  e.target.parentElement.childNodes[1].style.transform = `translateX(-${childHideDistance.value}px) translateY(0px)`;
                  childMoveDistance.value = childHideDistance.value;
              } else {
                  e.target.parentElement.childNodes[1].style.transform = `translateX(-${childMoveDistance.value}px) translateY(0px)`;
              }
          } else {
              // 鼠标向左
              let mouseMoveDistance = lastOffsetX.value - e.offsetX;
              let infoMoveDistance = mouseMoveDistance * Number(movePercent.value);
              childMoveDistance.value = childMoveDistance.value - infoMoveDistance;
              // console.log(167, '鼠标向左了开始', mouseMoveDistance, infoMoveDistance, childMoveDistance.value);
              if(e.offsetX > startPointToLeftDistance.value) {
                  // 鼠标还在起始点右侧
                  e.target.parentElement.childNodes[1].style.transform = `translateX(-${childMoveDistance.value}px) translateY(0px)`;
              } else {
                  // 鼠标在起始点或者移动到了起始点左侧
                  // console.log(173, '鼠标回到起始点');
                  e.target.parentElement.childNodes[1].style.transform = `translateX(0px)`;
              }
          }
      }
      lastOffsetX.value = e.offsetX;
    }
  }
});
  infoFather.value.addEventListener('mouseleave', (e) => {
    // console.log(183, '复位');
    startPointToLeftDistance.value = 0;
    fatherEleWidth.value = 0;
    childEleWidth.value = 0;
    startPointToRightDistance.value = 0;
    childHideDistance.value = 0;
    lastOffsetX.value = 0;
    e.target.parentElement.childNodes[1].style.transform = 'translateX(0px)';
    childMoveDistance.value = 0;
  });
})

function handleCancelTask() {
  emits('tableRowClickBack', props.rowData)
}
</script>

<style lang="scss" scoped>
.component-row-item {
  width: 100%;
  background: #fff;
  padding-left: 24px;
  display: flex;

  .left-info, .right-btn {
    display: inline-grid;
    font-family: my-fonts-normal;
    font-size: 14px;
    line-height: 50px;
  }
  .left-info {
    position: relative;
    width: calc(100% - 130px);
    margin-right: 10px;
    padding: 0 30px 0 0;
    white-space: nowrap;
    overflow: hidden;
    color: #818181;
    z-index: 1;
    .left-mask {
      position: absolute;
      left: 0px;
      width: 0px;
      height: 98%;
      background: linear-gradient(to right, rgba(255,255,255,1), rgba(255,255,255,0.5));
      z-index: 3;
      transition: all 300ms;
    }
    .right-mask {
      position: absolute;
      right: -2px;
      width: 30px;
      height: 98%;
      z-index: 3;
      background: linear-gradient(to left, rgba(255,255,255,1), rgba(255,255,255,0.5));
    }
    .name-info {
      transition: all 0.2s;
    }
    .mask-all {
      position: absolute;
      width: 100%;
      height: 100%;
      left: 0;
      top: 0;
      right: 0;
      bottom: 0;
      z-index: 5;
    }
  }
  .right-btn {
    width: 120px;
    height: 98%;
    color: #000;
    cursor: pointer;
    z-index: 3;
    background: #fff;
  }
}
</style>
