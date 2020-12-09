<template>
  <el-card>
    <el-form label-width="80px">
      <div v-show="activeStep === 0">
        <el-form-item label="广告名称">
          <el-input v-model="promotionAdDTO.name"></el-input>
        </el-form-item>
        <el-form-item label="广告位置">
          <el-select v-model="promotionAdDTO.spaceId" placeholder="请选择">
            <el-option
              v-for="item in advertSpace"
              :key="item.id"
              :label="item.name"
              :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="开始时间">
          <el-date-picker
            v-model="promotionAdDTO.startTime"
            type="date"
            placeholder="选择日期时间"
            value-format="yyyy-MM-dd"
          />
        </el-form-item>
        <el-form-item label="到期时间">
          <el-date-picker
            v-model="promotionAdDTO.endTime"
            type="date"
            placeholder="选择日期时间"
            value-format="yyyy-MM-dd"
          />
        </el-form-item>
        <el-form-item label="上线/下线">
          <el-radio v-model="promotionAdDTO.status" :label="1">上线</el-radio>
          <el-radio v-model="promotionAdDTO.status" :label="0">下线</el-radio>
        </el-form-item>
        <el-form-item label="广告图片">
          <course-image
            v-model="promotionAdDTO.img"
            :limit="5"
          />
        </el-form-item>
        <el-form-item label="广告链接">
          <el-input v-model="promotionAdDTO.link"></el-input>
        </el-form-item>
        <el-form-item label="广告备注">
          <el-input
            type="textarea"
            :rows="2"
            placeholder="请输入内容"
            v-model="promotionAdDTO.text">
          </el-input>
        </el-form-item>
      </div>
      <el-form-item>
        <el-button @click="handleSave">提交</el-button>
        <el-button @click="reset">重置</el-button>
      </el-form-item>
    </el-form>
  </el-card>
</template>

<script lang="ts">
import Vue from 'vue'
import {
  getCourseById
} from '@/services/course'
import {
  saveOrUpdateCourse,
  getAllSpaces
} from '@/services/advert'
import CourseImage from './CourseImage.vue'
import moment from 'moment'

export default Vue.extend({
  name: 'CreateOrUpdateCourse',
  props: {
    isEdit: {
      type: Boolean,
      default: false
    },
    courseId: {
      type: [String, Number]
    }
  },
  components: {
    CourseImage
  },
  data () {
    return {
      advertSpace: [],
      activeStep: 0,
      steps: [
        { title: '基本信息', icon: 'el-icon-edit' },
        { title: '课程封面', icon: 'el-icon-edit' },
        { title: '销售信息', icon: 'el-icon-edit' },
        { title: '秒杀活动', icon: 'el-icon-edit' },
        { title: '课程详情', icon: 'el-icon-edit' }
      ],
      promotionAdDTO: {
        name: '',
        spaceId: '',
        keyword: '',
        htmlContent: '',
        text: '',
        img: '',
        link: '',
        startTime: '',
        endTime: '',
        createTime: '',
        updateTime: '',
        status: 0,
        priority: 0,
        startTimeFormatString: '',
        endTimeFormatString: ''
      }
    }
  },
  created () {
    if (this.isEdit) {
      this.loadCourse()
    }
    this.loadAdvertSpace()
  },
  methods: {
    async loadCourse () {
      const { data } = await getCourseById(this.courseId)
      const { activityCourseDTO } = data.data
      if (activityCourseDTO) {
        activityCourseDTO.beginTime = moment(activityCourseDTO.beginTime).format('YYYY-MM-DD')
        activityCourseDTO.endTime = moment(activityCourseDTO.endTime).format('YYYY-MM-DD')
      }
      this.promotionAdDTO = data.data
    },

    async loadAdvertSpace () {
      const { data } = await getAllSpaces()
      this.advertSpace = data.content
    },

    reset () {
      console.log(1)
    },

    async handleSave () {
      const { data } = await saveOrUpdateCourse({ promotionAdDTO: this.promotionAdDTO })
      if (data.code === '000000') {
        this.$message.success('保存成功')
        this.$router.push('/advert')
      } else {
        this.$message.error('保存失败')
      }
    }
  }
})
</script>

<style lang="scss" scoped>
.el-step {
  cursor: pointer;
}
</style>
