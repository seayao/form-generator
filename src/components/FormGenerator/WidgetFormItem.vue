<template>
  <el-form-item
    v-if="element && element.key"
    :class="{active: selectWidget.key === element.key, 'is_req': element.options.required}"
    :label="element.name"
    class="widget-view"
    @click.native="handleSelectWidget(index)">
    <template v-if="element.type === 'input'">
      <el-input
        v-model="element.options.defaultValue"
        :style="{width: element.options.width}"
        :placeholder="element.options.placeholder"/>
    </template>

    <template v-if="element.type === 'textarea'">
      <el-input
        v-model="element.options.defaultValue"
        :rows="5"
        :style="{width: element.options.width}"
        :placeholder="element.options.placeholder"
        type="textarea" />
    </template>

    <template v-if="element.type === 'number'">
      <el-input-number
        v-model="element.options.defaultValue"
        :disabled="element.options.disabled"
        :controls-position="element.options.controlsPosition"
        :style="{width: element.options.width}"/>
    </template>

    <template v-if="element.type === 'radio'">
      <el-radio-group v-model="element.options.defaultValue" :style="{width: element.options.width}">
        <el-radio
          v-for="(item, index) in element.options.options"
          :key="item.value + index"
          :label="item.value"
          :style="{display: element.options.inline ? 'inline-block' : 'block'}">
          {{ element.options.showLabel ? item.label : item.value }}
        </el-radio>
      </el-radio-group>
    </template>

    <template v-if="element.type === 'checkbox'">
      <el-checkbox-group v-model="element.options.defaultValue" :style="{width: element.options.width}">
        <el-checkbox
          v-for="(item, index) in element.options.options"
          :key="item.value + index"
          :style="{display: element.options.inline ? 'inline-block' : 'block'}"
          :label="item.value">
          {{ element.options.showLabel ? item.label : item.value }}
        </el-checkbox>
      </el-checkbox-group>
    </template>

    <template v-if="element.type === 'time'">
      <el-time-picker
        v-model="element.options.defaultValue"
        :is-range="element.options.isRange"
        :placeholder="element.options.placeholder"
        :start-placeholder="element.options.startPlaceholder"
        :end-placeholder="element.options.endPlaceholder"
        :readonly="element.options.readonly"
        :disabled="element.options.disabled"
        :editable="element.options.editable"
        :clearable="element.options.clearable"
        :arrowControl="element.options.arrowControl"
        :style="{width: element.options.width}">
      </el-time-picker>
    </template>

    <template v-if="element.type === 'date'">
      <el-date-picker
        v-model="element.options.defaultValue"
        :type="element.options.type"
        :is-range="element.options.isRange"
        :placeholder="element.options.placeholder"
        :start-placeholder="element.options.startPlaceholder"
        :end-placeholder="element.options.endPlaceholder"
        :readonly="element.options.readonly"
        :disabled="element.options.disabled"
        :editable="element.options.editable"
        :clearable="element.options.clearable"
        :style="{width: element.options.width}">
      </el-date-picker>
    </template>

    <template v-if="element.type === 'rate'">
      <el-rate
        v-model="element.options.defaultValue"
        :max="element.options.max"
        :disabled="element.options.disabled"
        :allow-half="element.options.allowHalf"/>
    </template>

    <template v-if="element.type === 'color'">
      <el-color-picker
        v-model="element.options.defaultValue"
        :disabled="element.options.disabled"
        :show-alpha="element.options.showAlpha"/>
    </template>

    <template v-if="element.type === 'select'">
      <el-select
        v-model="element.options.defaultValue"
        :disabled="element.options.disabled"
        :multiple="element.options.multiple"
        :clearable="element.options.clearable"
        :placeholder="element.options.placeholder"
        :style="{width: element.options.width}">
        <el-option v-for="item in element.options.options" :key="item.value" :value="item.value" :label="element.options.showLabel?item.label:item.value"/>
      </el-select>
    </template>

    <template v-if="element.type === 'switch'">
      <el-switch v-model="element.options.defaultValue" :disabled="element.options.disabled"/>
    </template>

    <template v-if="element.type === 'slider'">
      <el-slider
        v-model="element.options.defaultValue"
        :min="element.options.min"
        :max="element.options.max"
        :disabled="element.options.disabled"
        :step="element.options.step"
        :show-input="element.options.showInput"
        :range="element.options.range"
        :style="{width: element.options.width}"/>
    </template>

    <template v-if="element.type === 'imgupload'">
      <fm-upload
        v-model="element.options.defaultValue"
        :disabled="element.options.disabled"
        :style="{'width': element.options.width}"
        :width="element.options.size.width"
        :height="element.options.size.height"
        token="xxx"
        domain="xxx"/>
    </template>

    <template v-if="element.type === 'blank'">
      <div style="height: 50px;color: #999;background: #eee;line-height:50px;text-align:center;">自定义区域</div>
    </template>
    <el-button v-if="selectWidget.key === element.key" title="删除" class="widget-action-delete" circle plain type="danger" @click.stop="handleWidgetDelete(index)">
      <icon name="regular/trash-alt" style="width: 12px;height: 12px;"/>
    </el-button>
    <el-button v-if="selectWidget.key === element.key" title="复制" class="widget-action-clone" circle plain type="primary" @click.stop="handleWidgetClone(index)">
      <icon name="regular/clone" style="width: 12px;height: 12px;"/>
    </el-button>
  </el-form-item>
</template>

<script>
import FmUpload from './Upload'
export default {
  components: {
    FmUpload
  },
  props: ['element', 'select', 'index', 'data'],
  data() {
    return {
      selectWidget: this.select
    }
  },
  watch: {
    select(val) {
      this.selectWidget = val
    },
    selectWidget: {
      handler(val) {
        this.$emit('update:select', val)
      },
      deep: true
    }
  },
  methods: {
    handleSelectWidget(index) {
      this.selectWidget = this.data.list[index]
    },
    handleWidgetDelete(index) {
      if (this.data.list.length - 1 === index) {
        if (index === 0) {
          this.selectWidget = {}
        } else {
          this.selectWidget = this.data.list[index - 1]
        }
      } else {
        this.selectWidget = this.data.list[index + 1]
      }

      this.$nextTick(() => {
        this.data.list.splice(index, 1)
      })
    },
    handleWidgetClone(index) {
      let cloneData = {
        ...this.data.list[index],
        options: { ...this.data.list[index].options },
        key: Date.parse(new Date()) + '_' + Math.ceil(Math.random() * 99999)
      }
      if (this.data.list[index].type === 'radio' || this.data.list[index].type === 'checkbox') {
        cloneData = {
          ...cloneData,
          options: {
            ...cloneData.options,
            options: cloneData.options.options.map(item => ({ ...item }))
          }
        }
      }
      this.data.list.splice(index, 0, cloneData)
      this.$nextTick(() => {
        this.selectWidget = this.data.list[index + 1]
      })
    }
  }
}
</script>
