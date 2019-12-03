<template>
  <div>
    <Modal
    v-model="isOnRecommend"
    class="recommend"
    width="65"
    :mask-closable="false"
    @on-visible-change="stateChange">
      <div v-if="show" class="slide" style="width: 100%">
        <template>
          <Carousel :autoplay="true" v-model="imgIndex" arrow="always" dots="outside" :autoplay-speed="3000">
            <CarouselItem v-for="item in imgList" :key="item.appId">
              <div class="appLogoBox">
                <img :src="item.appLogo" class="appLogo">
              </div>
            </CarouselItem>
          </Carousel>
      </template>
      </div>
      <div slot="footer"></div>
    </Modal>
  </div>
</template>

<script>
import { getRecommend } from '@/api/anniversary'
export default {
  props: {
    anniversaryData: Object,
    isOnRecommend: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      show: false,
      imgIndex: 0,
      imgList: []
    }
  },
  created () {
    this.getRecommend()
  },
  methods: {
    async getRecommend () {
      const res = await getRecommend()
      if (res.data.data.length > 0) {
        // 必须要加，否则图片不显示
        this.$nextTick(() => {
          this.show = true
        })
        this.imgList = res.data.data
      }
    },
    stateChange () {
      this.isOnRecommend = false
    }
  }
}
</script>

<style>
  .recommend .ivu-modal-content {
    background: transparent;
    box-shadow: none;
  }
  .recommend .ivu-modal-close .ivu-icon-ios-close {
    color: #ffffff;
    padding-right: 0px;
  }
  .recommend .ivu-modal-body{
    padding: 0;
    height: 78vh;
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .recommend .ivu-modal{
    top: 70px;
  }
  .recommend .ivu-carousel-dots-inside {
    /* display: none; */
  }
</style>
<style scoped>
  .appLogoBox {
    text-align: center;
    height: 50%;
    line-height: 1;
  }
  .appLogoBox img{
    max-width: 500px;
    cursor: pointer;
  }
</style>
