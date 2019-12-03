<template>
  <div class="school-main">
    <div id="bgp">
      <img :src="image" class="bg-image">
    </div>
    <div class="school-wrap">
      <div class="over-view">
        <div class="campus-bg">
          <img src="../../assets/img/anniversary/campus-bg.png" class="campusbg">
          <div class="jump-btn">
            <img :src="campus_image" class="jumpBtnImg" @click="jumpTo">
          </div>
        </div>
        <div class="campuslist">
          <p v-for="(campus, index) in campusList" :key="index" @click="clickCampus(index)" :class="currentIndex==index?'active':''">{{campus.name}}</p>
        </div>
        <iframe
        :src="roamUrl"
        height="100%"
        width="100%"
        scrolling="no"
        ></iframe>
      </div>
    </div>
  </div>
</template>

<script>
import {
  getState,
  queryCampus
} from '@/api/anniversary'
export default {
  data () {
    return {
      image: null,
      campus_image: null,
      roamUrl: null,
      cmipsPcUrl: null,
      campusList: [],
      hasCampus: false,
      currentIndex: 0
    }
  },
  created () {
    this.init()
  },
  methods: {
    init () {
      const data = {storeName: 'skinAndBackgroundConfiguration', key: 'saIndexIcon'}
      getState(data).then(res => {
        // console.log('res', res)
        if (res.data !== null) {
          this.image = baseDataApiUrl + res.data.data.value
        }
      })
      const campusData = {storeName: 'skinAndBackgroundConfiguration', key: 'cmGisRedirectIconPc'}
      getState(campusData).then(res => {
        if (res.data !== null) {
          this.campus_image = baseDataApiUrl + res.data.data.value
        }
      })
      const cmipsData = {storeName: 'runConfiguration', key: 'cmipsPcUrl'}
      getState(cmipsData).then(res => {
        // console.log('res', res)
        if (res.data !== null) {
          this.cmipsPcUrl = res.data.data.value
        }
      })
      queryCampus().then((res) => {
        // 排除校区列表无值的情况
        if (res.data.data.length > 0) {
          this.hasCampus = true
          // 没有全景地址的校区不展示
          this.campusList = res.data.data.filter(item => item.roamUrl)
          this.campusList.forEach((item, index) => {
            if (item.df) {
              this.currentIndex = index
              this.roamUrl = item.roamUrl
            }
          })
        }
      })
    },
    clickCampus (val) {
      this.currentIndex = val
      // 展示对应校区的全景
      this.roamUrl = this.campusList[val].roamUrl
    },
    jumpTo () {
      // 跳转至位置服务平台
      if (this.cmipsPcUrl === null) {
        this.$Message.info('cmips校庆专版地址为空，请后台配置')
      } else {
        // window.location.href = this.cmipsPcUrl
        // 先跳本地调试
        this.$router.push('/')
      }
    }
  }
}
</script>
<style scoped>
@import "../../assets/css/anniversary/anniversary.css";
</style>
