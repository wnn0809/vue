<template>
<div class="swiper-main">
  <div class="swiperWrapper">
    <div class="allFloor" :class="{allActive: flag}" @click="chooseAll($event)" slot>全部</div>
    <swiper :options="swiperOption" ref="floorSwiper">
      <swiper-slide
        class="item"
        v-for="(item, index) in dataList"
        :class="currentIndex==index?'activeFloor':''"
        @click="chooseFloor(index)"
        :key="index"
        >
        {{ item }}
      </swiper-slide>
    </swiper>
    <!-- 翻页放到swiper外面防止分页器触发on事件 -->
    <div class="swiper-button-next" slot="button-next"></div>
    <div class="swiper-button-prev" slot="button-prev"></div>
  </div>
</div>
</template>

<script>
import 'swiper/dist/css/swiper.css'
import { swiper, swiperSlide } from 'vue-awesome-swiper'
let vm = null
export default {
  components: {
    swiper,
    swiperSlide
  },
  data () {
    return {
      swiperOption: {
        notNextTick: true,
        observer:true,
        observeParents:false,
        slidesPerView: 6,
        // 设置循环时on事件索引错乱
        // loop: true,
        navigation: {
          nextEl: '.swiper-button-next',
          prevEl: '.swiper-button-prev'
        },
        on: {
          click: function () {
            // console.log(this)
            const realIndex = this.clickedIndex
            vm.chooseFloor(realIndex)
          }
        },
        preventLinksPropagation: false
        // spaceBetween: 20
      },
      dataList: [
        '负一楼',
        '一楼',
        '二楼',
        '三楼',
        '四楼',
        '五楼',
        '六楼',
        '七楼',
        '八楼',
        '九楼',
        '十楼',
        '十一楼',
        '十二楼',
        '十三楼',
        '十四楼',
        '十五楼',
        '十六楼',
        '十七楼',
        '十八楼'
      ],
      currentIndex: -2,
      flag: true
    }
  },
  computed: {
    swiper () {
      return this.$refs.floorSwiper.swiper
    }
  },
  created () {
    vm = this
  },
  methods: {
    chooseFloor (index) {
      this.flag = false
      this.currentIndex = index
      console.log('index', index)
      this.$emit('clickedFloor', index-1)
    },
    chooseAll (event) {
      this.flag = true
      this.currentIndex = -2
      console.log('event', event)
      this.$emit('clickedFloor', '')
    }
  }
}
</script>

<style>
.swiper-main {
  margin-top: 50px;
  height: 100px;
  width: 465px;
  background: #ffffff;
  border-radius: 5px;
}
.swiperWrapper {
  height: 36px;
  line-height: 36px;
  padding: 30px 20px 30px 100px;
  color: #666;
  font-size: 14px;
  overflow: hidden;
  position: relative;
}
.swiper-container {
  position: unset;
}
.allFloor {
  position: absolute;
  left: 35px;
  width:64px;
  font-size: 14px;
  color: #666;
  text-align: center;
  cursor: pointer;
}
.allActive {
  height:28px;
  line-height: 28px;
  background:rgba(107,205,155,1);
  border-radius:2px;
  color: #ffffff;
  margin-top: 4px;
}
.item {
  text-align: center;
  cursor: pointer;
}
.activeFloor{
  height:28px;
  line-height: 28px;
  margin-top: 4px;
  background:rgba(107,205,155,1);
  border-radius:2px;
  color: #ffffff;
    /*border-bottom: 2px solid #0a96eb;*/
  }
.swiper-button-next, .swiper-container-rtl .swiper-button-prev {
  right: 10px;
  width: 25px;
  height: 22px;
  line-height: 22px;
  margin-top: -12px;
  /* 去除翻页图标边框 */
  outline: none;
  /* 修改图标颜色 */
  background-image: url("data:image/svg+xml;charset=utf-8,%3Csvg%20xmlns%3D'http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg'%20viewBox%3D'0%200%2027%2044'%3E%3Cpath%20d%3D'M27%2C22L27%2C22L5%2C44l-2.1-2.1L22.8%2C22L2.9%2C2.1L5%2C0L27%2C22L27%2C22z'%20fill%3D'%23B5B5B5'%2F%3E%3C%2Fsvg%3E")
}
.swiper-button-prev, .swiper-container-rtl .swiper-button-next {
  left: 10px;
  width: 25px;
  height: 22px;
  margin-top: -12px;
  outline: none;
  background-image: url("data:image/svg+xml;charset=utf-8,%3Csvg%20xmlns%3D'http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg'%20viewBox%3D'0%200%2027%2044'%3E%3Cpath%20d%3D'M0%2C22L22%2C0l2.1%2C2.1L4.2%2C22l19.9%2C19.9L22%2C44L0%2C22L0%2C22L0%2C22z'%20fill%3D'%23B5B5B5'%2F%3E%3C%2Fsvg%3E")
}
</style>
