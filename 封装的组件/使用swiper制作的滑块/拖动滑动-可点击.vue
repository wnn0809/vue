<template>
    <div class="index">
      <div class="content-main">
        <div class="swiperItem">
          <swiper :options="swiperOption" ref="mySwiper">
            <!-- 幻灯内容 -->
            <swiper-slide
              class="item"
              v-for="(dateItem, index) in dateList"
              :class="currentIndex==index?'active':''"
              @click="chooseDate(index)"
              :key="index"
            >
              {{dateItem}}
            </swiper-slide>
          </swiper>
        </div>
        <div class="classStatus">
          <p style="font-size: 12px;color: #aaaaaa;margin-top: 7px;">节次</p>
          <div class="ulItem">
            <ul>
              <li
                v-for="(classItem, index) in classList"
                :key="index"
                v-bind:class="{currentClass: classItem.currentClass}"
                @click="chooseClass(classItem)"
              >
                <span v-if="!classItem.currentClass">{{classItem.class}}</span>
                <span v-else>p</span>
              </li>
            </ul>
          </div>
        </div>
      </div>
      <floor-slide @clickedFloor="getFloor"></floor-slide>
    </div>
</template>

<script>
import 'swiper/dist/css/swiper.css'
import { swiper, swiperSlide } from 'vue-awesome-swiper'
import floorSlide from '../view/floorSlide'

let vm = null

export default {
  name: 'index',
  components: {
    swiper,
    swiperSlide,
    floorSlide
  },
  created () {
    vm = this
  },
  data () {
    return {
      currentIndex: 0,
      swiperOption: {
        notNextTick: true,
        slidesPerView: 5,
        // loop: true,
        on: {
          click: function () {
            console.log(this)
            // const realIndex = this.realIndex
            const realIndex = this.clickedIndex
            // vm.chooseDate(vm.dateList[realIndex])
            vm.chooseDate(realIndex)
          }
        }
        // spaceBetween: 20
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
        '周一(08.13)',
        '周一(08.14)',
        '周一(08.15)',
        '周一(08.16)',
        '周一(08.17)',
        '周一(08.18)',
        '周一(08.19)'
      ],
      classList: [
        {class: '1', currentClass: false},
        {class: '2', currentClass: false},
        {class: '3', currentClass: false},
        {class: '4', currentClass: false},
        {class: '5', currentClass: false},
        {class: '6', currentClass: true},
        {class: '7', currentClass: false},
        {class: '8', currentClass: false},
        {class: '9', currentClass: false},
        {class: '10', currentClass: false},
        {class: '11', currentClass: false},
        {class: '12', currentClass: false},
        {class: '13', currentClass: false}
      ]
    }
  },
  computed: {
    swiper () {
      return this.$refs.mySwiper.swiper
    }
  },
  methods: {
    chooseDate (val) {
      this.currentIndex = val
    },
    chooseClass (val) {
      console.log('选中节次', val.class)
      this.classList.forEach(item => {
        if (val.class === item.class) {
          val.currentClass = true
        } else {
          item.currentClass = false
        }
      })
    },
    getFloor (val) {
      console.log('get the floor from children', val)
    }
  }
}
</script>

<style scoped>
  .index {
    height: 400px;
    background: bisque;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
  }
  .content-main {
    height: 100px;
    width: 465px;
    background: #ffffff;
    border-radius: 5px;
  }
  .swiperItem {
    height: 36px;
    line-height: 36px;
    border-bottom: 2px solid #cccccc;
    padding: 0px 8px;
  }
  .swiperItem .item {
    display: inline-block;
    width: 60px;
    font-size: 13px;
    float: left;
    text-align: left;
    cursor: pointer;
  }
  .active{
    color: #0a96eb!important;
    /*border-bottom: 2px solid #0a96eb;*/
  }
  .active:after {
    content: '';
    position: absolute;
    bottom: 0px;
    top: auto;
    left: 17px;
    height: 2px;
    width: 25px;
    background-color: #0a96eb;
  }
  .classStatus {
    padding: 0px 8px;
  }
  .ulItem {
    height: 20px;
    border-radius: 10px;
    background: rgb(116, 205, 255);
    color: #ffffff;
    margin-top: -9px;
  }
  .classStatus li {
    list-style: none;
    float: left;
    width: 32px;
    text-align: center;
    border-left: 1px solid;
    cursor: pointer;
  }
  .classStatus li:nth-child(1) {
    margin-left: -28px;
    border-left: none;
  }
  .currentClass {
    background: #ffa841;
  }
</style>
