<template>
  <BaseAuth
    :config="settings"
    enable-field="AUTH_WECOM"
    v-on="$listeners"
  />
</template>

<script>
import BaseAuth from './Base'
import { JsonEditor, UpdateToken } from '@/components/Form/FormFields'
import { JsonRequiredUserNameMapped } from '@/components/Form/DataForm/rules'

export default {
  name: 'WeCom',
  components: {
    BaseAuth
  },
  data() {
    const vm = this
    return {
      settings: {
        url: '/api/v1/settings/setting/?category=wecom',
        hasDetailInMsg: false,
        moreButtons: [
          {
            title: this.$t('WeComTest'),
            loading: false,
            callback: function(value, form, btn) {
              btn.loading = true
              vm.$axios.post(
                '/api/v1/settings/wecom/testing/',
                value
              ).then(res => {
                vm.$message.success(res['msg'])
              }).catch(() => {
                vm.$log.error('err occur')
              }).finally(() => { btn.loading = false })
            }
          }
        ],
        encryptedFields: ['WECOM_SECRET'],
        fields: [
          'AUTH_WECOM', 'WECOM_CORPID', 'WECOM_AGENTID',
          'WECOM_SECRET', 'WECOM_RENAME_ATTRIBUTES'
        ],
        fieldsMeta: {
          WECOM_SECRET: {
            component: UpdateToken
          },
          WECOM_RENAME_ATTRIBUTES: {
            component: JsonEditor,
            rules: [JsonRequiredUserNameMapped]
          }
        },
        // 不清理的话，编辑secret，在删除提交会报错
        cleanFormValue(data) {
          if (!data['WECOM_SECRET']) {
            delete data['WECOM_SECRET']
          }
          return data
        },
        submitMethod() {
          return 'patch'
        }
      }
    }
  },
  methods: {
  }
}
</script>

<style scoped>

</style>
