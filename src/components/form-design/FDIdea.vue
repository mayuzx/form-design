<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-09-30 12:28:12
 * @LastEditTime: 2019-10-14 10:51:10
 * @LastEditors: Please set LastEditors
 -->
<template>
  <el-form label-position="top" label-width="80px" size="mini">
    <el-form-item label="标题">
      <el-input v-model="data.title" size="small"></el-input>
    </el-form-item>
    <el-form-item label="表字段code">
      <el-input v-model="data.code" size="small"></el-input>
    </el-form-item>
    <el-form-item label="宽度">
      <el-input v-model="data.options.width" size="small"></el-input>
    </el-form-item>
    <el-form-item label="最大长度">
      <el-input-number
        v-model="data.options.maxlength"
        :min="1"
        :max="300"
        label="最大长度"
        size="small"
      ></el-input-number>
    </el-form-item>
    <el-form-item label="占位内容">
      <el-input v-model="data.options.placeholder" size="small"></el-input>
    </el-form-item>
    <el-form-item label="默认值">
      <el-select
        v-model="data.options.defaultValue"
        allow-create
        title="自定义值按回车确定"
        placeholder="请输入选择默认值"
        size="small"
      >
        <el-option
          v-for="(item, i) in defaultValueList"
          :key="i"
          :label="item.label"
          :value="item.val"
        ></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="数据绑定key">
      <el-input v-model="data.key" size="small" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="是否必填">
      <el-switch v-model="data.options.required" active-color="#13ce66" inactive-color="#EEEEEE"></el-switch>
    </el-form-item>
    <el-form-item label="是否禁用">
      <el-switch v-model="data.options.disabled" active-color="#13ce66" inactive-color="#EEEEEE"></el-switch>
    </el-form-item>
  </el-form>
</template>

<script>
import common from '@/utils/common'
import bus from '@/utils/bus'

export default {
  props: {
    propData: {
      type: Object,
      default: function () {
        return {
          title: '意见框落款',
          type: 'idea',
          code: '',
          icon: '/static/img/form-design/textarea.png',
          options: {
            width: '100%',
            defaultValue: '',
            required: false,
            disabled: false,
            placeholder: '',
            regEx: '',
            maxlength: 300
          },
          key: common.getGuid()
        }
      }
    }
  },
  data () {
    return {
      data: {
        title: '意见框落款',
        type: 'idea',
        code: '',
        icon: '/static/img/form-design/textarea.png',
        options: {
          width: '100%',
          defaultValue: '',
          required: false,
          disabled: false,
          placeholder: '',
          regEx: '',
          maxlength: 300
        },
        key: common.getGuid()
      }
    }
  },
  methods: {},
  mounted () {},
  watch: {
    data: {
      handler: function (value, oldValue) {
        let newFormList = common.deepClone(
          this.$store.state.formDesign.formList
        )
        // eslint-disable-next-line no-unused-vars
        let activeIndex
        for (let i = 0; i < this.$store.state.formDesign.formList.length; i++) {
          const element = newFormList[i]
          if (element.type !== 'grid') {
            if (element.key === this.$store.state.formDesign.activeKey) {
              activeIndex = i

              newFormList[i] = value
              this.$store.commit('formDesign/updateActiveKey', element.key)
              this.$store.dispatch(
                'formDesign/setFormList',
                common.deepClone(newFormList)
              )
              bus.$emit('formDesign.syncList', common.deepClone(newFormList))
              break
            }
          } else {
            for (let j = 0; j < element.cols.length; j++) {
              const element2 = element.cols[j]
              for (let k = 0; k < element2.list.length; k++) {
                const element3 = element2.list[k]
                if (element3.key === this.$store.state.formDesign.activeKey) {
                  activeIndex = i

                  newFormList[i].cols[j].list[k] = value
                  this.$store.commit(
                    'formDesign/updateActiveKey',
                    element3.key
                  )
                  this.$store.dispatch(
                    'formDesign/setFormList',
                    common.deepClone(newFormList)
                  )
                  bus.$emit(
                    'formDesign.syncList',
                    common.deepClone(newFormList)
                  )
                  break
                }
              }
            }
          }
        }
      },
      deep: true
    },
    propData: {
      handler: function (value) {
        this.data = common.deepClone(value)
      },
      deep: true
      // immediate: true
    }
  }
}
</script>

<style>
</style>
