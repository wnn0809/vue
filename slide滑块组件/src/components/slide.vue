<template>
    <div class="slideBox" id="slideBox">
      <div
        class="slideItem"
      >
        <ul class="slideBar">
          <li
            v-for="(dateItem,index) in dateList"
            :key="index"
            @click="chooseDate(index)"
            :style="slideStyle"
            @mousedown="start($event)"
            @mousemove="move($event)"
            @mouseup="end($event)"
            :class="currentIndex==index?'active':''">
            {{dateItem}}
          </li>
        </ul>
      </div>
    </div>
</template>

<script>
export default {
  data () {
    return {
      currentIndex: 0,
      isDown: false,
      startX: 0,
      endX: 0,
      moveX: 0,
      left: 0,
      slideStyle: {
        left: 0,
        position: 'relative'
      },
      dateList: [
        '周一(今天)',
        '周二(08.06)',
        '周三(08.07)',
        '周四(08.08)',
        '周五(08.09)',
        '周六(08.10)',
        '周日(08.11)',
        '周一(08.12)',
        '周二(08.13)',
        '111111',
        '222222',
        '33333',
        '444444'
      ]
    }
  },
  methods: {
    start (e) {
      e.stopPropagation()
      // 判断鼠标按下
      this.isDown = true
      this.startX = e.clientX
      console.log('鼠标按下', this.startX)
    },
    move (e) {
      if (this.isDown) {
        this.moveX = e.clientX - this.startX
        this.slideStyle.left = this.left + this.moveX + 'px'
        console.log('鼠标移动', this.slideStyle.left)
      }
    },
    end (e) {
      this.isDown = false
      this.endX = e.clientX
      this.left = this.endX - this.startX
      // console.log('鼠标弹起', this.endX, this.slideStyle.left)
    },
    chooseDate (val) {
      console.log('val', val)
      this.currentIndex = val
    }
  }
}
</script>

<style scoped>
  .slideBox {
    position: relative;
    width: 500px;
    height: 100px;
  }
  .slideItem {
    width: auto;
    position: relative;
  }
  .slideBar {
    white-space: nowrap;
    overflow-x: auto;
    border-bottom: 1px solid #cccccc;
    padding: 0px 5px;
  }
  li {
    display: inline-block;
    height: 50px;
    width: 90px;
    padding: 0px 2px;
    line-height: 50px;
    cursor: move;
  }
  /*滚动条样式*/
  .slideBar::-webkit-scrollbar {/*滚动条整体样式*/
    width: 0px;     /*高宽分别对应横竖滚动条的尺寸*/
    height: 1px;
  }
  .slideBar::-webkit-scrollbar-thumb {/*滚动条里面小方块*/
    border-radius: 5px;
    -webkit-box-shadow: inset 0 0 5px rgba(0,0,0,0.2);
    background: rgba(0,0,0,0.2);
  }
  .slideBar::-webkit-scrollbar-track {/*滚动条里面轨道*/
    -webkit-box-shadow: inset 0 0 5px rgba(0,0,0,0.2);
    border-radius: 0;
    background: rgba(0,0,0,0.1);
  }
  .active{
    /*background: #CB1E36;*/
    color: green!important;
    border-bottom: 2px solid red;
  }

</style>
