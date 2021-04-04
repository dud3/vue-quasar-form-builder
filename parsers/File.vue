<template>
<div class="col-12">
  <file-btn :label="field.label" :onSuccess="onFileSuccess" :required="field.required" :scope="field.label" :url="field.value" />
  <parser v-if="field.fields.length > 0" :ref="`node-${field.id}`" :pfields="field.fields" :groupId="groupId" :show="false" class="q-pa-sm" />
</div>
</template>

<script>
import Parser from './../Parser'

export default {
  name: 'File',
  extends: Parser,
  components: {
    Parser: () => import('./../Parser'),
    FileBtn: () => import('./../../generic/builders/FileBtn')
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
    },
    onFileSuccess (file, scope) {
      this.field.value = file.url
      this.$forceUpdate()
    }
  }
}
</script>
