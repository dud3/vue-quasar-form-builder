<template>
<div class="col-12">
 <q-input
    :label="field.label"
    :rules="[val => (val && val.length > 0 || !field.required) || `${field.label} Required`]"
    :readonly="disabled"
    v-model="field.value"
    @click="onClick"
    outlined
    hide-bottom-space
  >
    <template v-slot:append>
      <q-icon name="mdi-chevron-right" color="secondary" />
    </template>
  </q-input>

  <q-dialog v-model="dialog.show" position="right" :maximized="true">
    <q-card style="width: 432px;">
      <q-bar class="text-white text-center" :class="'bg-primary'" style="height: 66px;">
        <div><h6>{{ field.label }}</h6></div>
        <q-space />
        <q-btn dense flat icon="close" @click="dialog.show = false">
          <q-tooltip content-class="bg-white text-primary">Close</q-tooltip>
        </q-btn>
      </q-bar>

      <q-form @submit="save(field)" ref="form" class="q-pa-md">
        <parser ref="formParser" :pfields="field.fields" :show="true" />

        <div class="col-12 text-center q-pt-sm">
          <q-btn type="submit" label="Save" color="secondary" class="sh-btn-primary" />
        </div>
      </q-form>
    </q-card>
  </q-dialog>
</div>
</template>

<script>
import Parser from './../Parser'

export default {
  name: 'Equipment',
  extends: Parser,
  components: {
    Parser: () => import('./../Parser')
  },
  data () {
    return {
      dialog: {
        show: false
      }
    }
  },
  methods: {
    onClick () {
      this.dialog.show = true

      setTimeout(() => {
        this.$refs.formParser.fieldsDisable(this.disabled)
      }, 400)
    },
    save (field) {
      this.field.value = field.fields[0].value
      this.dialog.show = false
    },
    parentSubmit (field, node) {
      console.log(field, node)
    }
  }
}
</script>
