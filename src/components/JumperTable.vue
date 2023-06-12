<template>
  <v-container>
    <v-table>
      <td v-for="category in categories" :key="category">
        <v-table height="300px">
          <thead>
            <tr>
              <th class="text-left">
                <v-chip
                  class="ma-2"
                  color="primary"
                >
                {{ category }}
                </v-chip>
              </th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="setting in settings.filter(x => x.category == category)" :key="setting.name" @click.stop="jumpToCell(setting)">
              <td>{{ setting.name }}</td>
            </tr>
          </tbody>
        </v-table>
      </td>
    </v-table>
  </v-container>
</template>

<script>
export default {
  name: "JumperTable",

  data: () => ({
    settings:[],
    categories:[],
  }),

  methods: {
        load(){
          let settingJson = localStorage.getItem('settings');
          this.settings = JSON.parse(settingJson);
          this.categories = [...new Set(this.settings.map(x => x.category))];
        },
        async jumpToCell(setting){
          // eslint-disable-next-line
          let [tab] = await chrome.tabs.query({ active: true, currentWindow: true });
          // eslint-disable-next-line
          chrome.scripting.executeScript({
            target: { tabId: tab.id },
            // eslint-disable-next-line
            function: (address) => {
              console.log(address);
              document.getElementById("t-name-box").value = address;
              let KEvent = new KeyboardEvent( "keydown", { keyCode: 13 });
              document.getElementById("t-name-box").dispatchEvent( KEvent );
            },
            args: [setting.address]
          });
        },
  },
  mounted () {
    this.load();
  }
};
</script>
