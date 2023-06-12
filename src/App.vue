<template>
  <v-app>
    <v-main>
      <v-card>
        <v-tabs
          v-model="tab"
          bg-color="primary">
          <v-tab value="jumper">Jumper</v-tab>
          <v-tab value="setting">Setting</v-tab>
        </v-tabs>

    <v-card-text>
      <v-window v-model="tab">
        <v-window-item value="jumper">
          <JumperTable/>
        </v-window-item>
        <v-window-item value="setting">
          <SettingPage/>
        </v-window-item>
      </v-window>
    </v-card-text>
  </v-card>

    </v-main>
  </v-app>
</template>
<script>

import JumperTable from './components/JumperTable.vue'
import SettingPage from './components/SettingPage.vue'

export default {
  name: 'App',

  components: {
    JumperTable,
    SettingPage
  },

  data: () => ({
    tab: null,
  }),
  methods: {
  }
}

</script>
