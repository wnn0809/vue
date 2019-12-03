<template>
  <div>
    <Modal
        class="anniversary-info"
        v-model="isOpen"
        title=详情
        width="65%"
        @on-visible-change="stateChange"
        >
        <div class="info-wrap">
          <p class="title">{{anniversaryData.theme}}</p>
          <p class="sub-info">
            <span>
              发布时间： {{anniversaryData.updateTime&&anniversaryData.updateTime}}&nbsp;&nbsp;&nbsp;&nbsp;
            </span>
           <span v-if="anniversaryData.count">浏览次数： {{anniversaryData.count}}</span>
          </p>
          <p class="place-time">
            <span v-if="anniversaryData.place">活动地点：{{anniversaryData.place}}</span>
            <span>活动时间：{{anniversaryData.date&&anniversaryData.date}}&nbsp;{{anniversaryData.actTime&&anniversaryData.actTime}}</span>
          </p>
          <section class="content">
            <div v-html="anniversaryData.content"></div>
          </section>
        </div>
        <div slot="footer"></div>
    </Modal>
  </div>
</template>

<script>
export default {
  props: {
    anniversaryData: Object,
    isOpen: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
    }
  },
  created () {
    // 处理时间
    this.anniversaryData.updateTime = this.getTime(this.anniversaryData.updateTime)
  },
  methods: {
    change (t) {
      if (t < 10) {
        return '0' + t
      } else {
        return t
      }
    },
    getTime (gmtTime) {
      const d = new Date(gmtTime)
      const year = d.getFullYear()
      const month = this.change(d.getMonth() + 1)
      const day = this.change(d.getDate())
      const hour = this.change(d.getHours())
      const minute = this.change(d.getMinutes())
      const second = this.change(d.getSeconds())
      const time = year + '-' + month + '-' + day + ' ' + hour + ':' + minute + ':' + second
      // const time = String(year)+String(month)+String(day)+String(hour)+String(minute)+String(second);
      return time
    },
    stateChange () {
      this.isOpen = false
      this.$emit('updateModal', this.isOPen)
    }
  }
}
</script>

<style>
.anniversary-info .ivu-modal-header{
    border-radius: 6px 6px 0 0;
    background: #27aafa !important;
    color: #ffffff;
    height: 40px;
    display: flex;
    align-items: center;
  }
  .anniversary-info .ivu-modal-close {
    top: 2px;
  }
  .anniversary-info .ivu-modal-header-inner{
    color: #fff !important;
    font-size: 14px;
    font-weight: 200;
  }
  .anniversary-info .ivu-icon-ios-close{
    color: #fff !important;
  }
  .anniversary-info .ivu-modal-body{
    padding: 0;
    max-height: 78vh;
    overflow: hidden;
    overflow-y: auto;
  }
  .anniversary-info .ivu-modal-close .ivu-icon-ios-close:hover{
    color: #eee !important;
  }
  .anniversary-info .ivu-modal{
    top: 30px;
  }
</style>
<style scoped>
.info-wrap {
  width: 100%;
  padding: 15px 28px;
}
.info-wrap .title {
  font-size:18px;
  font-family:Microsoft YaHei;
  font-weight:400;
  color:rgba(50,50,50,1);
  text-align: center;
}
.info-wrap .sub-info {
  font-size:12px;
  font-family:Microsoft YaHei;
  font-weight:400;
  color:rgba(50,50,50,1);
  text-align: center;
  padding-top: 8px;
}
.info-wrap .place-time {
  font-size:14px;
  font-family:Microsoft YaHei;
  font-weight:400;
  color:rgba(50,50,50,1);
  padding-top:15px;
  }
  .info-wrap .place-time span:nth-child(2){
    padding-left: 20px;
  }
  .info-wrap .content {
    font-size:14px;
    font-family:Microsoft YaHei;
    font-weight:400;
    color:rgba(50,50,50,1);
    padding-top:15px;
  }
</style>
