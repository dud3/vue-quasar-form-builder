<template>
<div class="row">
  <div class="col-12">
    <div class="row q-pt-sm col-12" v-for="(field, index) in dfields" :key="index">
      <div class="col-1">
        <q-toggle v-model="field.required" :color="field.selectClass == 'Formula' ? 'info' : 'secondary'">
          <q-tooltip>Required</q-tooltip>
        </q-toggle>
      </div>
      <div class="col-2">
        <q-select @input="onClassChange(field, index)" outlined v-model="field.selectClass" :options="fclass" label="Class" />
      </div>
      <div class="col-2 q-pl-sm">
        <q-input outlined v-model="field.label" label="Label" />
      </div>
    </div>

    <div class="row col-12">
      <div class="col-12">
        <q-btn @click="addInput" class="q-ma-md" rounded color="white" text-color="dark" push icon="eva-plus-outline" label="Field" />
      </div>
    </div>
  </div>
</div>
</template>

<script>
// import helper from './../../../libs/helper'
import FormBuilder from './../Builder'

export default {
  name: 'Product',
  extends: FormBuilder,
  props: {
    fields: {
      type: Array,
      default: () => []
    },
  },
  data: () => {
    return {
      dfields: [],
    }
  },
  created () {
  },
  mounted () {
  },
  methods: {
    // Events
    onClassChange (field, index) {
      console.log(field)
    }
  }
}
</script>
