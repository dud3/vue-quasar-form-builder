<template>
<div class="row no-padding" v-if="mounted">
  <div v-for="(field, index) in fields" :key="index" v-show="field.show" class="q-mt-sm col-12">
    <field ref="node-parser" v-if="field.selectClass.value == 'Field'" :field="field" :groupId="groupId" :showAll="showAll" :bookingRef="bookingRef" />
    <formula ref="node-parser" v-if="field.selectClass.value == 'Formula'" :field="field" :groupId="groupId" :showAll="showAll" :bookingRef="bookingRef" />
    <dropdown ref="node-parser" v-if="field.selectClass.value == 'Dropdown'" :field="field" :groupId="groupId" :showAll="showAll" :bookingRef="bookingRef" />

    <file v-if="field.selectClass.value == 'File'" :field="field" :showAll="showAll" :bookingRef="bookingRef" />
    <displayfile v-if="field.selectClass.value == 'DisplayFile'" :field="field" :groupId="groupId" :showAll="showAll" :bookingRef="bookingRef" />

    <datepicker ref="node-parser" v-if="field.selectClass.value == 'Datepicker'" :field="field" :groupId="groupId" :showAll="showAll" :bookingRef="bookingRef" />
    <equipment ref="node-parser" v-if="field.selectClass.value == 'Equipment'" :field="field" :groupId="groupId" :showAll="showAll" :bookingRef="bookingRef" />

    <product ref="node-parser" v-if="field.selectClass.value == 'Product'" :field="field" :groupId="groupId" :showAll="showAll" :bookingRef="bookingRef" />
    <productDatepicker ref="node-parser" v-if="field.selectClass.value == 'ProductDatepicker'" :field="field" :groupId="groupId" :showAll="showAll" :bookingRef="bookingRef" />
  </div>
</div>
</template>

<script>
// import helper from './../../../libs/helper'

export default {
  name: 'Parser',
  components: {
    Field: () => import('./parsers/Field'),
    Formula: () => import('./parsers/Formula'),
    Dropdown: () => import('./parsers/Dropdown'),
    File: () => import('./parsers/File'),
    Displayfile: () => import('./parsers/Displayfile'),
    Datepicker: () => import('./parsers/Datepicker'),
    Equipment: () => import('./parsers/Equipment'),

    Product: () => import('./parsers/product/Product'),
    ProductDatepicker: () => import('./parsers/product/Datepicker')
  },
  props: {
    pfields: {
      type: Array,
      default: () => []
    },
    field: {
      type: Object,
      default: () => { return { id: -1 } }
    },
    showAll: { // ignore show prop below and show everything
      type: Boolean,
      default: () => false
    },
    show: {
      type: Boolean,
      default: () => true
    },
    onMounted: {
      type: Function,
      default: () => {}
    },
    onFieldClick: {
      type: Function,
      default: () => {}
    },
    onFieldSelect: {
      type: Function,
      default: () => {}
    },
    bookingRef: {
      type: String,
      default: () => ''
    },
    groupId: {
      type: String,
      default: () => '0'
    }
  },
  data () {
    return {
      fields: [],
      disabled: false,

      mounted: false
    }
  },
  created () {
    this.fields = this.pfields
    this.showFields(this.show)
  },
  mounted () {
    this.mounted = true
    this.onMounted(this.mounted)
  },
  methods: {
    setFields (fields) {
      this.fields = fields
      this.showFields(this.show)
    },
    getFields () {
      return this.fields
    },
    getFieldsStr () {
      return JSON.stringify(this.getFields())
    },
    showFields (show) {
      this.fields.map(f => { f.show = show })
    },
    parentFieldSelect (node, field) {
      // ... extend
    },
    childFieldSelect (node, field) {
      // ... extend
    },
    fieldSelect (node, field) {
      // ... extend
      const nodeParsers = node.$refs['node-parser'] || []
      nodeParsers.map(nodeParser => { nodeParser.parentFieldSelect(node, field) })
    },
    fieldsDisable (disable) {
      disable = disable === undefined ? false : disable

      let refs = this.$refs['node-parser']

      if (this.$refs[`node-${this.field.id}`]) refs = [this.$refs[`node-${this.field.id}`]]

      refs = refs || []

      refs.map(r => {
        r.disabled = disable
        r.fieldsDisable && r.fieldsDisable(disable)
      })
    },
    parentValidate () {
      // ... extend
    },
    validate () {
      let refs = this.$refs['node-parser']

      if (this.$refs[`node-${this.field.id}`]) refs = [this.$refs[`node-${this.field.id}`]]
      refs = refs || []

      return refs.filter(ref => ref.id !== -1).map(ref => {
        if (ref.validate) {
          return ref.validate(ref, this.field)
        }
      })
    },
    isValid () {
      return this.validate()
    },
    parentSubmit (node, field) {
      // ... extend
    },
    async submit (node) {
      let refs = this.$refs['node-parser']

      if (this.$refs[`node-${this.field.id}`]) refs = [this.$refs[`node-${this.field.id}`]]
      refs = refs || []

      refs.filter(ref => ref.id !== -1).map(ref => { ref.submit && ref.submit(ref, this.field) })
    }
  }
}
</script>
