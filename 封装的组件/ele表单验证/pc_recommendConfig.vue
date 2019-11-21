<template>
  <div class="app-container recommend-config">
    <el-form :inline="true" class="demo-form-inline" :model="pc_recommendConfigObj" ref="postForm">
            <div class="form-item" v-for="(item, index) in pc_recommendConfigObj.recommendConfig">
                <el-row :gutter="20">
                    <el-col :span="24">
                        <el-form-item
                         :label="'系统推荐名称:'"
                         :key="item.key"
                         :prop="'recommendConfig.' + index + '.appName'"
                         :rules="[{required: true, message: '这是必填项', trigger: 'blur'},
                         { max: 10, message: '不超过10个字', trigger: 'blur' }]">
                            <el-input v-model="item.appName" placeholder="请输入系统推荐名称" style="width:450px"></el-input>
                        </el-form-item>
                    </el-col>
                  </el-row>
                  <el-row>
                    <el-col :span="12">
                        <el-form-item
                         :label="'是否支持跳转:'"
                         :key="item.key"
                         
                         >
                          <el-radio v-model="item.supportJump" :label="true">支持</el-radio>
                          <el-radio v-model="item.supportJump" :label="false">不支持</el-radio>
                        </el-form-item>
                    </el-col>
                    <el-col :span="12">
                      <el-form-item
                      v-if="item.supportJump"
                      :label="'系统推荐地址:'"
                      :key="item.key"
                      :prop="'recommendConfig.' + index + '.appUrl'"
                      :rules="{pattern: /^(http|ftp|https):\/\/[\w\-_]+(\.[\w\-_]+)+([\w\-\.,@?^=%&:/~\+#]*[\w\-\@?^=%&/~\+#])?$/,
                        message: '请填写正确的地址', trigger: 'blur'}"
                      >
                      <el-input v-model="item.appUrl" type="text" placeholder="请输入系统推荐地址" style="width:450px"></el-input>
                      </el-form-item>
                    </el-col>
                  </el-row>
                  <el-row>
                    <el-col :span="12">
                        <el-form-item
                        :label="'推荐时段:'"
                        :key="item.key"
                        :prop="'recommendConfig.' + index + '.time'"
                        :rules="{required: true, message: '请选择推荐时段', trigger: 'blur'}"
                        >
                            <el-date-picker
                              v-model="item.time"
                              type="datetimerange"
                              :picker-options="pickerOptions2"
                              range-separator="至"
                              start-placeholder="开始日期"
                              end-placeholder="结束日期"
                              value-format="yyyy-MM-dd HH:mm:ss"
                              align="right">
                            </el-date-picker>
                        </el-form-item>
                    </el-col>
                    <el-col :span="12">
                        <el-form-item
                        :label="'排序:'"
                        label-width="110px"
                        :key="item.key"
                        :prop="'recommendConfig.' + index + '.orderId'"
                        :rules="{required: true, pattern: /^[1-9]\d*$/, message: '请填写正整数', trigger: 'blur'}"
                        >
                          <el-input
                           v-model.number="item.orderId"
                           type="text"
                           placeholder="请输入排序"
                           style="width:450px"
                          ></el-input>
                        </el-form-item>
                    </el-col>
                </el-row>
                <el-row :gutter="20">
                    <div class="upload-box">
                        <el-form-item
                        :label="'上传主题图片:'"
                        :key="item.key"
                        :prop="'recommendConfig.' + index + '.appLogo'"
                        :rules="{required: true, message: '请上传图片'}"
                        >
                         <el-upload
                            class="avatar-uploader"
                            action=""
                            accept=".jpg,.png,.gif"
                            :http-request="handleCompassIcon(item)"
                            :before-upload="acceptFile(['jpg','png','gif'],250)"
                            :show-file-list="false">
                            <img v-if="item.appLogo" :src="item.appLogo" class="avatar">
                            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                            <div slot="tip" class="el-upload__tip">建议尺寸:536 X 352px，大小不超过250kb，支持格式：jpg、png、gif</div>
                        </el-upload>
                        </el-form-item>
                    </div>
                </el-row>
                <el-row>
                    <el-button type="danger" icon="el-icon-delete" style="float: right;" @click="deleteRecommend(index)">删除该推荐系统</el-button>
                </el-row>
            </div>
        </el-form>

        <el-row :gutter="20" style="">
            <el-col>
                <el-button type="primary" icon="el-icon-plus" @click="addRecommend">添加推荐系统</el-button>
                <el-button type="primary" :loading="submitting" @click="submit">提  交</el-button>
            </el-col>
        </el-row>
  </div>
</template>

<script>
import { addRecommendList, getRecommendListQuery, delRecommend } from '@/api/recommendConfig'
export default {
  name: 'recommend-config',
  data() {
    return {
      pickerOptions2: {
        shortcuts: [{
          text: '最近一周',
          onClick(picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 7)
            picker.$emit('pick', [start, end])
          }
        }, {
          text: '最近一个月',
          onClick(picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 30)
            picker.$emit('pick', [start, end])
          }
        }, {
          text: '最近三个月',
          onClick(picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 90)
            picker.$emit('pick', [start, end])
          }
        }]
      },
      pc_recommendConfigObj: {
        recommendConfig: []
      },
      dynamicValidateForm: {
        domains: [{
          value: ''
        }]
      },
      submitting: false,
      informationConfig: {},
      validate: {
        appName: [{ required: true, message: '请输入应用名' }]
      }
    }
  },
  methods: {
    handleCompassIcon(item) {
      return function(params) {
        var reader = new FileReader()
        reader.readAsDataURL(params.file)
        reader.onload = (e) => {
          item.appLogo = e.target.result
        }
      }
    },
    addRecommend() {
      if (this.pc_recommendConfigObj.recommendConfig.length < 5) {
        this.pc_recommendConfigObj.recommendConfig.push({
          'appLogo': '',
          'appName': '',
          'appUrl': '',
          'endTime': '',
          'memo': '',
          'orderId': '',
          'startTime': '',
          'appId': '',
          'key': Date.now(),
          'supportJump': false
        })
      } else {
        this.$message({
          type: 'error',
          message: '最多可推荐5个！'
        })
      }
    },
    initData() {
      getRecommendListQuery().then(response => {
        this.pc_recommendConfigObj.recommendConfig = response.data.data
        this.pc_recommendConfigObj.recommendConfig.forEach((val) => {
          val.supportJump = !!val.supportJump
          this.$set(val, 'time', [val.startTime, val.endTime])
          this.$set(val, 'key', Date.now())
        })
        // console.log('response,', this.pc_recommendConfigObj.recommendConfig)
      })
    },
    deleteRecommend(index) {
      this.$confirm('确定要删除该推荐系统?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        const id = this.pc_recommendConfigObj.recommendConfig[index].appId
        if (id) {
          delRecommend(id).then(res => {
            this.$message({
              type: 'success',
              message: '删除成功！'
            })
            this.initData()
          })
        } else {
          this.pc_recommendConfigObj.recommendConfig.splice(index, 1)
        }
      })
    },
    submit() {
      if (this.pc_recommendConfigObj.recommendConfig.length > 0) {
        this.$refs['postForm'].validate(valid => {
          if (valid) {
            this.pc_recommendConfigObj.recommendConfig.forEach((val) => {
              val.startTime = val.time[0]
              val.endTime = val.time[1]
              delete val.time
              delete val.key
            })
            this.submitting = true
            addRecommendList(this.pc_recommendConfigObj.recommendConfig).then(res => {
              this.submitting = false
              this.$message({
                type: 'success',
                message: '成功！'
              })
              this.initData()
            })
          } else {
            this.$message({
              type: 'error',
              message: '请先完善表单！'
            })
            return false
          }
        })
      }
    }
  },
  created() {
    this.initData()
  }
}
</script>

<style lang='scss'>
    .recommend-config{
        .config-manager .el-form-item__content .el-input{
            width: 100%;
        }
        padding: 0;
        .bg-color{
            padding: 20px;
            padding-left: 40px;
            border: 1px solid rgb(220, 223, 230);
            box-shadow: 1px 4px 6px rgba(0, 0, 0,.08);
            margin-bottom: 20px;
            background: #fff;
        }
        .check-box{
            border: 1px solid rgb(220, 223, 230);
            margin-bottom: 30px;
            padding-left: 20px;
            .el-form-item{
                margin: 10px 0;
            }
            .item-check{
                display: inline-block !important;
            }
            .title{
                display: inline-block;
                width: 150px;
                margin: 20px 0;
                font-size: 14px;
            }
        }
        .rotate-input{
            width: 36px !important;
            .el-input__inner{
                padding: 0;
            }
        }
        .autoPaly{
            position: absolute;
            right: 20px;
            bottom: 100px;
        }
        .border{
            border: 1px solid #d9d9d9;
            flex: 1;
        }
        .rotate-box{
            display: flex;
            height: 242px;
            .tip{
                width: 22%;
                padding: 20px;
                padding-bottom: 40px;
                text-align: center;
                p{
                    margin: 0;
                }
                .el-icon-refresh{
                    font-size: 100px;
                    line-height: 130px;
                    color: #CAD3DF;
                }
            }
            .input-box{
                flex: 1;
                padding:24px;
                height: 242px;
                overflow: auto;
                .title{
                    width: 16%;
                    padding-right: 6px;
                    overflow: hidden;
                    white-space: nowrap;
                    text-overflow: ellipsis;
                }
                .check-box{
                    display: flex;
                    margin-bottom: 14px;
                }
                .el-form-item{
                    flex: 1;
                }
                ul,li{
                    list-style: none;
                    padding: 0;
                    margin: 0;
                }
                li:last-child .check-box{
                    margin: 0;
                }
            }
        }
        .el-form--label-top .el-form-item__label{
            padding: 0;
        }
        .el-upload-dragger{
            height: 147px;
        }
        .form-item{
            border: 1px solid rgb(220, 223, 230);
            padding: 20px;
            box-shadow: 1px 4px 6px rgba(0, 0, 0,.08);
            margin-bottom: 20px;
            background: #fff;
        }
        .label{
            text-align: center;
        }
        .default-lang{
            display: flex;
            padding-top: 2em;
            .el-form-item__label{
                width: 140px;
            }
        }
        .el-form--inline .el-form-item:not(.default-lang){
            display: block;
        }
        .upload-img-box{
            padding: 0;
            border: 1px solid rgb(220, 223, 230);
            border-right: 0;
            text-align: center;
        }
        .upload-item{
            border:1px solid rgb(220, 223, 230);
            padding: 20px 0;
        }
        .upload-box{
            position: relative;
            width: 100%;
            padding: 0 20px;
            margin-bottom: 10px;
            float: left;
            color: #606266;
            &.ms{
                .el-upload{
                    width: 60%;
                }
                .el-upload-dragger{
                    width: 100%;
                }
            }
            p{
                margin: 0;
                margin-bottom: 10px;
            }
        }
        .avatar-uploader .el-upload {
            border: 1px dashed #d9d9d9;
            border-radius: 6px;
            cursor: pointer;
            position: relative;
            overflow: hidden;
        }
        .avatar-uploader .el-upload:hover {
            border-color: #409EFF;
        }
        .avatar-uploader-icon {
            font-size: 28px;
            color: #8c939d;
            width: 146px;
            height: 146px;
            line-height: 146px;
            text-align: center;
        }
        .avatar {
            width: 146px;
            height: 146px;
            display: block;
        }
    }

</style>
