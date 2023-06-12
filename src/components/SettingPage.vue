<style scoped>
 .v-text-field{
      min-width: 100px;
}
</style>
<template>
    
  <v-container>
    <v-btn @click.stop="edit()">
        <v-icon color="indigo-darken-3">mdi-pen</v-icon>
        {{ editButtonText }}
    </v-btn>
    <v-btn class="ml-3" @click.stop="exportfile()">
        <v-icon color="indigo-darken-3">mdi-export</v-icon>
        設定出力
    </v-btn>
    <v-table :hover="true" height="300px">
      <thead>
        <tr>
          <th class="text-left">Category</th>
          <th class="text-left">Name</th>
          <th class="text-left">Address</th>
          <th class="text-left">Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="setting in settings" :key="setting.name">
          <td v-if="!isEdit">{{ setting.category }}</td>
          <td v-else>
            <v-text-field v-model="setting.category" width="100px" required></v-text-field>
          </td>
          <td v-if="!isEdit">{{ setting.name }}</td>
          <td v-else>
            <v-text-field v-model="setting.name" width="100px" required></v-text-field>
          </td>
          <td v-if="!isEdit">{{ setting.address }}</td>
          <td v-else>
            <v-text-field v-model="setting.address" width="100px" required></v-text-field>
          </td>
          <td>
            <v-icon @click.stop="deleteSetting(settings.indexOf(setting))">
              mdi-delete
            </v-icon>
          </td>
        </tr>
      </tbody>
    </v-table>
    <div class="mt-5">
        <v-text-field v-model="category" label="カテゴリ" required></v-text-field>
        <v-text-field v-model="name" label="表示名" required></v-text-field>
        <v-text-field v-model="address" label="セル番地" required></v-text-field>
        <v-btn @click.stop="add()" :disabled="isDisableAddButton">
            <v-icon> mdi-plus-circle </v-icon>
            追加
        </v-btn>
        <v-file-input
            v-model="files"
            class="mt-3"
            accept="json/*"
            label="設定ファイルインポート"
            color="deep-purple-accent-4"
            @change="onChange"
        ></v-file-input>
    </div>
  </v-container>
</template>

<script>
export default {
  name: "SettingPage",

  data: () => ({
    category: "",
    name: "",
    address: "",
    isEdit:false,
    files: null,
    file: null,
    editButtonText:"編集",
    settings: [],
  }),

  computed: {
    isDisableAddButton: function () {
      return !(this.category && this.name && this.address);
    },
  },

  methods: {
    load() {
      let settingJson = localStorage.getItem("settings");
      if(settingJson){
        this.settings = JSON.parse(settingJson);
      }
    },
    add() {
      let setting = {
        category: this.category,
        name: this.name,
        address: this.address,
      };
      this.settings.unshift(setting);

      this.save();
    },
    exportfile() {
        // JSON ファイルを表す Blob オブジェクトを生成
        const json = JSON.stringify(this.settings);
        const blob = new Blob([json], { type: 'application/json' });
        var link = document.createElement('a');
        // ファイル名を GAS から取得する
        link.download = 'JumperSetting.json';
        link.href = window.URL.createObjectURL(blob);
        link.click();
    },
    save() {
      localStorage.setItem("settings", JSON.stringify(this.settings));
    },
    deleteSetting(index) {
      this.settings.splice(index, 1);
      this.save();
    },
    edit(){
        if(this.isEdit) this.save();
        this.load();
        this.isEdit = !this.isEdit;
        this.editButtonText = this.isEdit ? '変更を保存' : '編集する';
    },
    onChange() {
        this.file = this.files[0];
        console.log(this.file);
        console.log(this.file.type);
        if (!this.file.type.match('json.*')) {
            alert('please select JumperSetting file');
            return;
        }

        var fr = new FileReader();
        const _this = this;
        fr.onload = function(e) { 
            console.log(e);
            let result = JSON.parse(e.target.result);
            console.log(result);
            _this.settings = result;
            _this.save();
        }

        fr.readAsText(this.file);
    },
  },
  mounted() {
    this.load();
  },
};
</script>
