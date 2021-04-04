<template>
<div class="col-12">
  <q-input type="text" v-model="field.value" :label="field.label" :readonly="disabled" outlined
    :rules="[val => (val && val.length > 0 || !field.required) || `${field.label} Required`]" hide-bottom-space
  />
  <parser v-if="field.fields.length > 0" :ref="`node-${field.id}`" :pfields="field.fields" :groupId="groupId" :show="false" class="q-pa-sm" />
</div>
</template>

<script>
import Parser from './../Parser'

export default {
  name: 'Field',
  extends: Parser,
  components: {
    Parser: () => import('./../Parser')
  },
  props: {
    groupId: {
      type: String,
      default: () => '0'
    }
  },
  data () {
    return {
    }
  },
  created () {
  },
  methods: {
    parentFieldSelect (node, field) {
      node.showFields(true)
    },
    fieldSelect (field) {
      const node = this.$refs[`node-${field.id}`]
      if (node) node.fieldSelect(node, field)
    }
  }
}
</script>
