<style scoped>
</style>

<template>
  <q-file
    v-model="file.select"
    outlined
    ref="file"
    :label="this.label.length > 0 ? this.label : 'Add attachment'"
    @input="handleFileUpload">
     <template v-slot:file>
      {{ res.url }}
     </template>
      <template v-slot:prepend>
        <q-icon name="mdi-paperclip" />
      </template>

      <template v-slot:after>
        <q-btn round dense flat icon="launch" @click="helper.router.tor(res.url)"></q-btn>
      </template>
  </q-file>
</template>

<script>
import fileSvc from './../../../../http/services/file.js'
import helper from './../../../../libs/helper.js'

export default {
  name: 'FileBtn',
  props: {
    file: {
      type: Object,
      default: () => {
        return {
          select: [],
          data: null,
          loading: false
        }
      }
    },
    scope: {
      type: String,
      default: ''
    },
    label: {
      type: String,
      default: ''
    },
    url: {
      type: String,
      default: ''
    },
    onSuccess: {
      type: Function,
      default: () => {}
    },
    onFail: {
      type: Function,
      default: () => {}
    },
    fileProps: {
      type: Object,
      default: () => {
        return {
          table_name: 'not_a_table',
          table_key_id: 0,
          category: 'generic'
        }
      }
    },
    required: {
      type: Boolean,
      default: () => false
    }
  },

  data: () => {
    return {
      helper,

      res: {}
    }
  },

  created () {
    if (this.url.length > 0) this.res.url = this.url
  },

  mounted () {
    this.$refs.file.value = [' '] // todo: fix me
  },

  methods: {
    handleFileUpload () {
      if (this.file.select !== undefined) {
        this.$q.loading.show()
        fileSvc.upload(this.file.select, this.fileProps).then((r) => {
          this.res = r.data.result
          this.onSuccess(r.data.result, this.scope)
        }).catch((e) => {
          this.onFail(e)
        }).then(() => { this.$q.loading.hide() })
      }
    }
  }
}
</script>
