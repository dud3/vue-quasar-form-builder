<template>
<div class="row">
  <div class="col-12">
    <div class="row q-pt-sm col-12" v-for="(field, index) in dfields" :key="index">

      <div class="col-1">
        <q-toggle v-model="field.required" :color="field.selectClass.value == 'Formula' ? 'info' : 'secondary'">
          <q-tooltip>Required</q-tooltip>
        </q-toggle>
      </div>

      <div class="col-2">
        <q-select @input="onClassChange(field, index)" outlined v-model="field.selectClass" :options="fclass" label="Class" />
      </div>

      <div class="col-2 q-pl-sm">
        <q-input outlined v-model="field.label" label="Label" />
      </div>

      <div class="col-3 q-pl-sm" v-if="field.hasType">
        <q-select
          multiple
          outlined
          use-chips
          :max-values="maxCondition[field.selectClass.value]"
          @input="onTypeChange(field)"
          v-model="field.selectType"
          :options="ftypes[field.selectClass.value]"
          label="Type" />
      </div>

      <div class="col-2 q-pl-sm" v-show="field.selectClass.value == 'Dropdown'">
        <q-input outlined v-model="field.options" label="Options" />
      </div>

      <div class="col-2 q-pl-sm" v-show="(field.selectType.length == 1 ? condition[field.selectType].length > 0 : false)">
        <q-select
          multiple
          outlined
          use-chips
          v-model="field.condition"
          :options="condition[field.selectType]"
          label="Condition"
        />
        <q-input outlined v-model="field.value" label="Value" />
      </div>

      <div class="col-3 q-pl-sm" v-show="field.valueOptions != undefined ? field.valueOptions.length > 0 : false">
        <q-select
          multiple
          outlined
          use-chips
          v-model="field.value"
          :options="field.valueOptions"
          label="Options"
        />
      </div>

      <div class="col-2 q-pl-sm">
        <file-btn v-if="field.selectClass.value == 'DisplayFile'"
          class="q-pb-sm" label="Add image" :scope="String(index)" :url="field.value" :onSuccess="onFileSuccess" />
      </div>

      <div class="col-2 q-pl-sm">
        <q-btn color="red" icon="clear" round flat @click="dfields.splice(index, 1)" />
        <q-btn v-show="dfields.length > 1" color="primary" icon="keyboard_arrow_up" round flat :disable="index == 0" @click="_swap(index, index - 1)" />
        <q-btn v-show="dfields.length > 1" color="primary" icon="keyboard_arrow_down" round flat :disable="index == dfields.length - 1" @click="_swap(index, index + 1)" />
      </div>

      <div class="col-12 q-pl-lg" v-if="field.selectClass.value == 'Equipment'">
        <builder :ref="`node-${index}`" :pclass="['Dropdown', 'Field', 'DisplayFile', 'File', 'Formula', 'Datepicker']" />
      </div>

      <div class="col-12 q-pl-lg" v-if="field.selectClass.value == 'Datepicker'">
        <builder :ref="`node-${index}`" :pclass="['Datepicker']" :maxFields="1" />
      </div>

      <div class="col-12 q-pl-lg" v-if="field.selectClass.value == 'ProductDatepicker'">
        <builder :ref="`node-${index}`" :pclass="['ProductDatepicker', 'Product']" :maxFields="1" />
      </div>
    </div>

    <div v-show="dfields.length < maxFields" class="row col-12">
      <div class="col-12">
        <q-btn @click="addInput" class="q-ma-md" rounded color="white" text-color="dark" push icon="eva-plus-outline" label="Field" />
      </div>
    </div>
  </div>
</div>
</template>

<script>
import helper from './../../../libs/helper'
import FileBtn from './builders/FileBtn'

export default {
  name: 'Builder',
  components: {
    FileBtn
  },
  props: {
    fields: {
      type: Array,
      default: () => []
    },
    pclass: {
      type: Array,
      default: () => ['Field', 'Dropdown', 'DisplayFile', 'File', 'Formula', 'Datepicker', 'ProductDatepicker', 'Equipment']
    },
    maxFields: {
      type: Number,
      default: () => 128
    }
  },
  data: () => {
    return {
      helper,

      fclass: [
        { label: 'Short text', value: 'Field' },
        { label: 'Dropdown', value: 'Dropdown' },
        { label: 'Display File', value: 'DisplayFile' },
        { label: 'File Upload', value: 'File' },
        { label: 'Formula', value: 'Formula' },
        { label: 'Standalone Datepicker', value: 'Datepicker' },
        { label: 'Equipment', value: 'Equipment' },
        { label: 'Product', value: 'Product' },
        { label: 'Conditional Datepicker', value: 'ProductDatepicker' }
      ],

      ftypes: {
        Field: ['Any', 'Number', 'Email'],
        Dropdown: ['Any'],
        File: ['Any', 'Image', 'Pdf'],
        DisplayFile: ['Image', 'Pdf'],
        Formula: ['Number'],
        Datepicker: ['Single', 'Range'],
        Equipment: [],
        Product: [],
        ProductDatepicker: ['Single']
      },

      dfields: [],

      condition: {
        '': [],
        Any: [],
        Number: [
          { label: 'Equal', value: '==' },
          { label: 'Bigger', value: '>' },
          { label: 'Smaller', value: '<' },
          { label: 'Not', value: '!' }
        ],
        Email: [],
        Image: [],
        Pdf: [],
        Single: [],
        Range: []
      },

      maxCondition: {}
    }
  },
  created () {
    this.maxCondition = {
      Dropdown: 1,
      Field: 1,
      File: this.ftypes.File.length,
      DisplayFile: this.ftypes.DisplayFile.length,
      Datepicker: 1,
      Equipment: 1,
      Product: 1,
      ProductDatepicker: 1
    }

    this.fclass = this.fclass.filter(f => this.pclass.indexOf(f.value) > -1)
  },
  methods: {
    instance () {
      const instance = {
        id: helper.misc.randstr(8),
        show: true, // visible
        required: true,
        disabled: false,
        label: '',
        selectClass: this.fclass[0],
        hasType: true,
        selectType: [],
        options: '',
        condition: [],
        conditionValue: 0,
        maxCondition: 1,

        value: '',
        valueOptions: [],

        parent: null,
        fields: []
      }

      this.onClassChange(instance)

      return instance
    },
    addInput (field) {
      const fields = field.fields || this.dfields
      const instance = this.instance()
      instance.parent = field

      fields.push(instance)
    },

    setFields (fields) {
      this.dfields = fields

      this.$nextTick(() => {
        for (let i = 0; i < this.dfields.length; i++) {
          const ref = this.$refs[`node-${i}`] || []
          if (ref.length > 0) ref[0].setFields(this.dfields[i].fields)
        }
      })
    },

    getFields () {
      for (let i = 0; i < this.dfields.length; i++) {
        const ref = this.$refs[`node-${i}`] || []
        if (ref.length > 0) this.dfields[i].fields = ref[0].getFields()
      }

      return this.dfields
    },

    // Private

    _swap (i, j) {
      const tmp = this.dfields[i]
      this.dfields[i] = this.dfields[j]
      this.dfields[j] = tmp

      this.$forceUpdate()
    },

    // Events

    onClassChange (field, index) {
      field.hasType = field.selectClass.value !== 'Equipment' &&
        field.selectClass.value !== 'Product' &&
        field.selectClass.value !== 'ProductDatepicker'

      // note: quick solution
      // tood: refactor

      field.value = ''
      field.valueOptions = []
      field.selectType = ['Any']

      if (field.hasType) field.selectType = [this.ftypes[field.selectClass.value][0]]
    },

    onTypeChange (field) {
      const key = (field.selectType.length >= 1) ? field.selectType[0] : ''

      field.condition = [this.condition[key][0]]
    },

    onFileSuccess (file, index) {
      this.dfields[index].value = file.url
    }
  }
}
</script>
