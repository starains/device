<template>
  <q-page class="flex justify-center q-ma-md">
    <div style="width: 800px">
      <q-toolbar class="text-primary q-my-lg">
        <q-btn flat round dense icon="notification_important" />
        <q-toolbar-title>
          告警
        </q-toolbar-title>
      </q-toolbar>
      <q-card class="q-ma-md" v-if="alarmTypes !== null">
        <q-card-section>
          <q-list highlight separator >
            <q-item v-for="(alarmKey) in Object.keys(alarmTypes)" :key="alarmKey" >
              <q-item-section avatar v-if="$q.screen.gt.xs">
                <q-icon color="primary" name="notification_important" />
              </q-item-section>
              <q-item-section @click="goto(alarmKey)" class="cursor-pointer">
                <q-item-label >{{alarmKey}}</q-item-label>
                <q-item-label caption>{{alarmTypes[alarmKey].description}}</q-item-label>
              </q-item-section>
              <q-item-section side >
                <div class="text-grey-8 q-gutter-xs">
                  <q-btn color="secondary" size="12px" flat dense round icon="info" @click="goto(alarmKey)">
                    <q-tooltip>详情</q-tooltip>
                  </q-btn>
                  <q-btn color="secondary" size="12px" flat dense round icon="more" @click="gotoDeviceType">
                    <q-tooltip>定义</q-tooltip>
                  </q-btn>
                </div>
              </q-item-section>
            </q-item>
          </q-list>
        </q-card-section>
      </q-card>
    </div>
  </q-page>
</template>

<script>
export default {
  name: 'alarms',
  data () {
    return {
    }
  },
  computed: {
    alarmTypes () {
      let device = this.$store.getters.getCurrentUser
      return device.deviceType.alarmTypes
    }
  },
  methods: {
    goto (name) {
      var page = {
        name: 'alarm',
        params: { name: name }
      }
      this.$router.push(page)
    },
    gotoDeviceType () {
      var page = {
        name: 'devicetype'
      }
      this.$router.push(page)
    }
  }
}
</script>

<style>
</style>
