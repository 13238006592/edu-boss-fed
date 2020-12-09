<template>
  <el-card>
    <el-form label-width="80px">
      <div v-show="activeStep === 0">
        <el-form-item label="广告位名称">
          <el-input v-model="promotionAdDTO.name"></el-input>
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
  saveOrUpdateSpace,
  getAllSpaces,
  getSpaceById
} from '@/services/advert'
import moment from 'moment'

export default Vue.extend({
  name: 'CreateOrUpdateCourse',
  props: {
    isEdit: {
      type: Boolean,
      default: false
    },
    advertId: {
      type: [String, Number]
    }
  },
  components: {
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
        spaceKey: 'test-myx-spaceKey'
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
      const { data } = await getSpaceById(this.advertId)
      this.promotionAdDTO = data.content
    },

    async loadAdvertSpace () {
      const { data } = await getAllSpaces()
      this.advertSpace = data.content
    },

    reset () {
      console.log(1)
    },

    async handleSave () {
      const { data } = await saveOrUpdateSpace({ promotionSpaceDTO: this.promotionAdDTO })
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
