<template>
  <div class="schedule" v-if="onSchedule">
    <div class="date-box">
      <section class="flex">
        <p style="font-size:13px">活动日程</p>
        <label class="cancel" @click="cancelDate">x</label>
      </section>
      <div class="date-wrap" v-if="hasData">
        <CheckboxGroup v-model="checkedDates" @on-change="checkedDatesChange">
          <Checkbox
            :indeterminate="indeterminate"
            :value="checkAllDate"
            @click.prevent.native="handleCheckAll"
            style="margin-right:3px"
          >全选</Checkbox>
          <Checkbox v-for="(item,index) in dateList" :key="index" :label="item">{{ item }}</Checkbox>
        </CheckboxGroup>
      </div>
      <div class="noneDate" v-else>
        当前没有校庆活动日程
      </div>
    </div>
  </div>
</template>

<script>
import {
  queryDate,
  getMarkers
} from '@/api/anniversary'
import _ from '@/views/Global/Global.vue'
export default {
  props: {
    campusId: Number
  },
  data () {
    return {
      indeterminate: false,
      checkAllDate: false,
      checkedDates: [],
      dateList: [],
      onSchedule: true,
      groupId: 0,
      hasData: false
    }
  },
  created () {
    this.init()
  },
  methods: {
    async getMarkers () {
      const dates = this.checkedDates.join(',')
      const query = {campusId: this.groupId, dates: dates}
      let markersInfo = []
      const res = await getMarkers(query)
      if (res.data.data.length > 0) {
        markersInfo = res.data.data
        // 获取到日程信息后触发事件
        this.$emit('showMarks', markersInfo)
      } else {
        this.$emit('showMarks', [])
      }
    },
    init () {
      let target = _.campusList.find(
        item => item.zones.find(element => element.zoneId === this.campusId)
      )
      this.groupId = target.groupId
      const campusId = {campusId: this.groupId}
      queryDate(campusId).then((res) => {
        if (res.data.data.dates.length > 0) {
          this.dateList = res.data.data.dates
          this.hasData = true
        } else {
          this.hasData = false
        }
      })
    },
    handleCheckAll () {
      if (this.indeterminate) {
        this.checkAllDate = false
      } else {
        this.checkAllDate = !this.checkAllDate
      }
      this.indeterminate = false
      if (this.checkAllDate) {
        this.checkedDates = this.dateList
      } else {
        this.checkedDates = []
        this.onSchedule = false
      }
      this.getMarkers()
    },
    checkedDatesChange (data) {
      if (this.checkedDates.length === 0) {
        this.onSchedule = false
      }
      if (data.length === this.dateList.length) {
        this.indeterminate = false
        this.checkAllDate = true
      } else if (data.length > 0) {
        this.indeterminate = true
        this.checkAllDate = false
      } else {
        this.indeterminate = false
        this.checkAllDate = false
      }
      // 获取日程信息
      this.getMarkers()
    },
    cancelDate () {
      this.onSchedule = false
      this.$emit('showMarks', [])
    }
  }
}
</script>

<style scoped>
.schedule {
  position: fixed;
  top: 170px;
  right: 25px;
  transition: top 0.3s;
  z-index: 4;
}
.date-box {
  width: 552px;
  height: 105px;
  background: #ffffff;
  border-radius: 3px;
  box-shadow: 0 0 13px #cbc9c9;
  padding: 8px 15px;
  overflow-y: auto;
}
.date-box .flex {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.date-box .cancel {
  width: 20px;
  height: 20px;
  text-align: right;
  font-size: 14px;
  cursor: pointer;
}
.noneDate{
  font-size: 14px;
  display: flex;
  justify-content: center;
  align-items: center;
}
.date-wrap {
  margin: 5px 0px;
}
.date-wrap .ivu-checkbox-group-item {
  width: 18%;
  font-size: 12px;
  margin-bottom: 3px;
}
</style>
