<template>
  <q-page class="flex justify-center q-ma-md">
    <q-dialog v-model="showDialog" persistent>
      <q-card style="width: 800px; max-width: 80vw;">
      <q-toolbar class="text-primary">
        <q-toolbar-title>
          执行设备操作
        </q-toolbar-title>
        <q-space />
        <q-btn icon="close" flat round dense @click="$router.back()" />
      </q-toolbar>
      <q-card class="q-ma-md">
        <q-card-section>
          <q-list>
            <q-item>
              <q-item-section>
                <q-item-label caption>操作</q-item-label>
                <q-item-label>{{action}}</q-item-label>
              </q-item-section>
              <q-item-section side><q-btn color="primary" @click="submit">执行</q-btn></q-item-section>
            </q-item>
          </q-list>
          <q-card class="q-my-md">
              <q-expansion-item
                ref="req"
                default-opened
                class="q-ma-md"
                switch-toggle-side
                expand-separator
                header-class="text-primary"
                label="请求信息">
                <AttributeInput :attDefinition="actionType.request" v-if="show" edit="true" ref="request"/>
              </q-expansion-item>
            <q-separator />
              <q-expansion-item
                ref="response"
                class="q-ma-md"
                switch-toggle-side
                expand-separator
                header-class="text-primary"
                label="响应信息">
                  <DataValue :dataValue="value.value" />
              </q-expansion-item>
          </q-card>
        </q-card-section>
      </q-card>
    </q-card>
    </q-dialog>
  </q-page>
</template>

<script>
import DataValue from '../components/DataValue.vue'
import AttributeInput from '../components/AttributeInput.vue'
import { stomp } from '../components/stomp'
import { http } from '../components/http'
import store from '../store'

export default {
  name: 'actionDevice',
  components: {
    AttributeInput, DataValue
  },
  data () {
    return {
      showDialog: false,
      value: {},
      deviceId: '',
      action: '',
      actionType: {},
      show: false,
      opened: false
    }
  },
  created () {
    this.deviceId = this.$route.params.id
    this.action = this.$route.params.action
    this.getActionType(this.action)
    this.showDialog = true
  },
  beforeRouteLeave: (to, from, next) => {
    store.commit('close')
    next()
  },
  methods: {
    submit () {
      if (this.$refs.request.canSubmit()) {
        this.$q.dialog({
          title: '提交操作',
          message: '确认提交操作请求？',
          ok: {
            label: '确定'
          }
        }).onOk((data) => {
          let input = this.$refs.request.getInputValue()
          let keys = Object.keys(input)
          let inputValue = input[keys[0]]
          stomp.operate(this.deviceId, 'action', this.action, inputValue, (result) => {
            this.value = result
            this.opened = true
            this.$q.dialog({
              title: '操作成功',
              ok: {
                label: '确定'
              }
            }).onOk((data) => {
              this.$refs.req.hide()
              this.$refs.response.show()
            })
          }, () => {
            this.value = {}
          })
        })
      }
    },
    getActionType (action) {
      let deviceUrl = '/devices/' + this.deviceId
      http('get', deviceUrl, '', (response) => {
        this.actionType = response.data.deviceType.actionTypes[action]
        this.show = true
      })
    }
  }
}
</script>

<style>
</style>
