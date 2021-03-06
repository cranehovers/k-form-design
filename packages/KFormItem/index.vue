<!--
 * @Description: 传入record数据，通过判断record.type，来渲染对应的组件
 * @Author: kcz
 * @Date: 2020-01-02 22:41:48
 * @LastEditors: kcz
 * @LastEditTime: 2020-03-29 20:30:14
 -->
<template>
  <a-form-item
    v-if="
      [
        'input',
        'textarea',
        'date',
        'time',
        'number',
        'radio',
        'checkbox',
        'select',
        'rate',
        'switch',
        'slider',
        'uploadImg',
        'uploadFile'
      ].includes(record.type)
    "
    :label="record.label"
    :label-col="config.layout === 'horizontal' ? config.labelCol : {}"
    :wrapper-col="config.layout === 'horizontal' ? config.wrapperCol : {}"
  >
    <!-- 单行文本 -->
    <a-input
      :style="`width:${record.options.width}`"
      v-if="record.type === 'input'"
      :disabled="disabled || record.options.disabled"
      :placeholder="record.options.placeholder"
      :type="record.options.type"
      :allowClear="record.options.clearable"
      :maxLength="record.options.maxLength"
      v-decorator="[
        record.model, // input 的 name
        {
          initialValue: record.options.defaultValue, // 默认值
          rules: record.rules // 验证规则
        }
      ]"
    />
    <!-- 多行文本 -->
    <a-textarea
      :style="`width:${record.options.width}`"
      v-else-if="record.type === 'textarea'"
      :autoSize="{
        minRows: record.options.minRows,
        maxRows: record.options.maxRows
      }"
      :disabled="disabled || record.options.disabled"
      :placeholder="record.options.placeholder"
      :allowClear="record.options.clearable"
      :maxLength="record.options.maxLength"
      :rows="4"
      v-decorator="[
        record.model, // input 的 name
        {
          initialValue: record.options.defaultValue, // 默认值
          rules: record.rules // 验证规则
        }
      ]"
    />

    <!-- 日期选择 -->
    <KDatePicker
      v-else-if="record.type === 'date'"
      :record="record"
      :parentDisabled="disabled"
      v-decorator="[
        record.model, // input 的 name
        {
          initialValue: record.options.range
            ? record.options.rangeDefaultValue
            : record.options.defaultValue, // 默认值
          rules: record.rules // 验证规则
        }
      ]"
    />
    <!-- 时间选择 -->
    <KTimePicker
      v-else-if="record.type === 'time'"
      :record="record"
      :parentDisabled="disabled"
      v-decorator="[
        record.model, // input 的 name
        {
          initialValue: record.options.defaultValue, // 默认值
          rules: record.rules // 验证规则
        }
      ]"
    />
    <!-- 计数器 -->
    <a-input-number
      v-else-if="record.type === 'number'"
      :style="`width:${record.options.width}`"
      :min="record.options.min"
      :max="record.options.max"
      :disabled="disabled || record.options.disabled"
      :step="record.options.step"
      :placeholder="record.options.placeholder"
      v-decorator="[
        record.model,
        {
          initialValue: record.options.defaultValue,
          rules: record.rules
        }
      ]"
    />
    <!-- 单选框 -->
    <a-radio-group
      v-else-if="record.type === 'radio'"
      :options="
        !record.options.dynamic
          ? record.options.options
          : dynamicData[record.options.dynamicKey]
          ? dynamicData[record.options.dynamicKey]
          : []
      "
      :disabled="disabled || record.options.disabled"
      :placeholder="record.options.placeholder"
      v-decorator="[
        record.model,
        {
          initialValue: record.options.defaultValue,
          rules: record.rules
        }
      ]"
    />
    <!-- 多选框 -->
    <a-checkbox-group
      v-else-if="record.type === 'checkbox'"
      :options="
        !record.options.dynamic
          ? record.options.options
          : dynamicData[record.options.dynamicKey]
          ? dynamicData[record.options.dynamicKey]
          : []
      "
      :disabled="disabled || record.options.disabled"
      :placeholder="record.options.placeholder"
      v-decorator="[
        record.model,
        {
          initialValue: record.options.defaultValue,
          rules: record.rules
        }
      ]"
    />
    <!-- 评分 -->
    <a-rate
      v-else-if="record.type === 'rate'"
      :count="record.options.max"
      :disabled="disabled || record.options.disabled"
      :placeholder="record.options.placeholder"
      :allowHalf="record.options.allowHalf"
      v-decorator="[
        record.model,
        {
          initialValue: record.options.defaultValue,
          rules: record.rules
        }
      ]"
    />
    <!-- 下拉选框 -->
    <a-select
      :style="`width:${record.options.width}`"
      v-else-if="record.type === 'select'"
      :placeholder="record.options.placeholder"
      :showSearch="record.options.filterable"
      :options="
        !record.options.dynamic
          ? record.options.options
          : dynamicData[record.options.dynamicKey]
          ? dynamicData[record.options.dynamicKey]
          : []
      "
      :disabled="disabled || record.options.disabled"
      :allowClear="record.options.clearable"
      :mode="record.options.multiple ? 'multiple' : ''"
      v-decorator="[
        record.model,
        {
          initialValue: record.options.defaultValue,
          rules: record.rules
        }
      ]"
    />
    <!-- 开关 -->
    <a-switch
      v-else-if="record.type === 'switch'"
      :disabled="disabled || record.options.disabled"
      v-decorator="[
        record.model,
        {
          initialValue: record.options.defaultValue,
          valuePropName: 'checked',
          rules: record.rules
        }
      ]"
    />
    <!-- 滑块 -->
    <div
      v-else-if="record.type === 'slider'"
      :style="`width:${record.options.width}`"
      class="slider-box"
    >
      <div class="slider">
        <a-slider
          :disabled="disabled || record.options.disabled"
          :min="record.options.min"
          :max="record.options.max"
          :step="record.options.step"
          v-decorator="[
            record.model,
            {
              initialValue: record.options.defaultValue,
              rules: record.rules
            }
          ]"
        />
      </div>
      <div class="number" v-if="record.options.showInput">
        <a-input-number
          style="width:100%"
          :disabled="disabled || record.options.disabled"
          :min="record.options.min"
          :max="record.options.max"
          :step="record.options.step"
          v-decorator="[
            record.model,
            {
              initialValue: record.options.defaultValue
            }
          ]"
        />
      </div>
    </div>
    <!-- 上传图片 -->
    <UploadImg
      v-else-if="record.type === 'uploadImg'"
      :style="`width:${record.options.width}`"
      :parentDisabled="disabled"
      :record="record"
      v-decorator="[
        record.model,
        {
          initialValue: record.options.defaultValue,
          rules: record.rules
        }
      ]"
    />
    <!-- 上传文件 -->
    <UploadFile
      v-else-if="record.type === 'uploadFile'"
      :style="`width:${record.options.width}`"
      :record="record"
      :parentDisabled="disabled"
      v-decorator="[
        record.model,
        {
          initialValue: record.options.defaultValue,
          rules: record.rules
        }
      ]"
    />
  </a-form-item>
  <!-- 动态表格 -->
  <a-form-item
    v-else-if="record.type === 'batch'"
    :label="!record.options.showLabel ? '' : record.label"
    :label-col="
      config.layout === 'horizontal' && record.options.showLabel
        ? config.labelCol
        : {}
    "
    :wrapper-col="
      config.layout === 'horizontal' && record.options.showLabel
        ? config.wrapperCol
        : {}
    "
  >
    <KBatch
      ref="KBatch"
      :style="`width:${record.options.width}`"
      :record="record"
      :parentDisabled="disabled"
      :dynamicData="dynamicData"
      v-decorator="[
        record.model,
        {
          initialValue: record.options.defaultValue,
          rules: record.rules
        }
      ]"
    />
  </a-form-item>
  <!-- button按钮 -->
  <a-form-item
    v-else-if="record.type === 'button'"
    :wrapper-col="
      config.layout === 'horizontal'
        ? { ...config.wrapperCol, offset: config.labelCol.span }
        : {}
    "
  >
    <a-button
      :disabled="disabled || record.options.disabled"
      @click="
        record.options.handle === 'submit'
          ? false
          : record.options.handle === 'reset'
          ? $emit('handleReset')
          : dynamicData[record.options.dynamicFun]
          ? dynamicData[record.options.dynamicFun]()
          : false
      "
      :type="record.options.type"
      :html-type="record.options.handle === 'submit' ? 'submit' : undefined"
      v-text="record.label"
    ></a-button>
  </a-form-item>
  <!-- alert提示 -->
  <a-form-item v-else-if="record.type === 'alert'">
    <a-alert
      :message="record.label"
      :description="record.options.description"
      :type="record.options.type"
      :showIcon="record.options.showIcon"
      :closable="record.options.closable"
      :banner="record.options.banner"
    />
  </a-form-item>
  <!-- 文本 -->
  <a-form-item v-else-if="record.type === 'text'">
    <div :style="{ textAlign: record.options.textAlign }">
      <label
        :class="{ 'ant-form-item-required': record.options.showRequiredMark }"
        v-text="record.label"
      ></label>
    </div>
  </a-form-item>
  <!-- html -->
  <div
    v-else-if="record.type === 'html'"
    v-html="record.options.defaultValue"
  ></div>
  <!-- 自定义组件 -->
  <customComponent
    v-else-if="customList.includes(record.type)"
    :record="record"
    :config="config"
  />

  <div v-else>
    <!-- 分割线 -->
    <a-divider
      v-if="
        record.type === 'divider' &&
          record.label !== '' &&
          record.options.orientation
      "
      :orientation="record.options.orientation"
      >{{ record.label }}</a-divider
    >
    <a-divider v-else-if="record.type === 'divider' && record.label !== ''">{{
      record.label
    }}</a-divider>
    <a-divider v-else-if="record.type === 'divider' && record.label === ''" />
  </div>
</template>
<script>
/*
 * author kcz
 * date 2019-11-20
 */
// import moment from "moment";
import customComponent from "./customComponent";

import KBatch from "../KBatch";
import UploadFile from "../UploadFile";
import UploadImg from "../UploadImg";
import KDatePicker from "../KDatePicker";
import KTimePicker from "../KTimePicker";
export default {
  name: "KFormItem",
  props: {
    // 表单数组
    record: {
      type: Object,
      default: () => {}
    },
    // form-item 宽度配置
    config: {
      type: Object,
      required: true
    },
    dynamicData: {
      type: Object,
      default: () => {
        return {};
      }
    },
    disabled: {
      type: Boolean,
      default: false
    }
  },
  components: {
    KBatch,
    UploadImg,
    UploadFile,
    KDatePicker,
    KTimePicker,
    customComponent
  },
  computed: {
    customList() {
      if (window.$customComponentList) {
        return window.$customComponentList.map(item => item.type);
      } else {
        return [];
      }
    }
  },
  methods: {
    validationSubform() {
      // 验证动态表格
      if (!this.$refs.KBatch) return true;
      return this.$refs.KBatch.validationSubform();
    }
  }
};
</script>
<style lang="less" scoped>
.slider-box {
  display: flex;
  > .slider {
    flex: 1;
    margin-right: 16px;
  }
  > .number {
    width: 70px;
  }
}
</style>
