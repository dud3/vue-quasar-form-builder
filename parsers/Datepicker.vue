<template>
<div class="col-12">
  <q-input
    outlined
    :value="
      field.value && field.value.length == 0
      ? ''
      : (field.value && typeof field.value == 'object'
        ? helper.date.df(field.value.from) + ' - ' + helper.date.df(field.value.to)
        : field.value ? helper.date.df(field.value) : '')"
    :label="field.label"
    :rules="[val => (val && val.length > 0 || !field.required) || `${field.label} Required`]"
    :readonly="disabled"
    outline
    color="dark"
    no-caps
    shadow-1
    hide-bottom-space
    icon="eva-calendar-outline">
    <q-popup-proxy ref="date-proxy" transition-show="scale" transition-hide="scale">
      <q-date
        v-model="field.value"
        :range="field.selectType.filter(r => r == 'Range').length > 0"
        @input="fieldSelect(field); $refs['date-proxy'].hide()">
        <div class="row items-center justify-end q-gutter-sm">
          <q-btn label="OK" color="primary" flat v-close-popup />
        </div>
      </q-date>
    </q-popup-proxy>
  </q-input>

  <parser v-if="field.fields.length > 0" :ref="`node-${field.id}`" :pfields="field.fields" :groupId="groupId" :show="false || showAll" :bookingRef="bookingRef" />
</div>
</template>

<script>
import helper from './../../../../libs/helper'
import Parser from './../Parser'

export default {
  name: 'Datepicker',
  extends: Parser,
  components: {
    Parser: () => import('./../Parser')
  },
  data () {
    return {
      helper
    }
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
