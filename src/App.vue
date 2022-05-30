<template>
  <amplify-authenticator>
    <q-layout view="hhh lpr fff">
      <q-header reveal elevated class="glossy">
        <q-toolbar>
          <q-toolbar-title class="row">
            {{ appTitle }}
            <q-space />
            <q-btn-dropdown color="primary" icon="info" dropdown-icon="none" />
          </q-toolbar-title>
        </q-toolbar>
        <q-toolbar>
          {{ subtitle }}
        </q-toolbar>
      </q-header>
      <q-page-container>
        <q-tab-panels v-model="slide" animated>
          <q-tab-panel name="list" style="width: 95vw">
            <list />
          </q-tab-panel>
          <q-tab-panel name="add" style="width: 95vw">
            <add v-model="user" @setTopMessage="viewTopMessage" />
          </q-tab-panel>
        </q-tab-panels>
      </q-page-container>
      <q-footer reveal bordered class="bg-white text-primary">
        <q-tabs
          v-model="slide"
          no-caps
          active-color="primary"
          indicator-color="transparent"
          class="text-grey"
        >
          <q-tab name="list"> <q-icon name="list" />一覧 </q-tab>
          <q-tab name="add"> <q-icon name="add" />登録 </q-tab>
        </q-tabs>
      </q-footer>
    </q-layout>
    <top-message
      v-model="topDialog"
      v-model:icon="topIcon"
      v-model:iconColor="topIconColor"
      v-model:message="topMessage"
    />
    <amplify-sign-out></amplify-sign-out>
  </amplify-authenticator>
</template>

<script>
import { ref, onBeforeUnmount } from "vue";
import { Auth } from "aws-amplify";
import List from "./layouts/List.vue";
import Add from "./layouts/Add.vue";
import TopMessage from "./components/DialogTopMessage.vue";

export default {
  name: "LayoutDefault",
  components: {
    List,
    Add,
    TopMessage,
  },
  data() {
    return {
      slide: "list",
      appTitle: "ウマ娘 殿堂入り一覧ツール",
      subtitle: "アプリ版ウマ娘の殿堂入りウマ娘を管理します。",
      user: "",
      active: 0,
      topDialog: false,
      topIcon: "",
      topIconColor: "",
      topMessage: "",
    };
  },
  setup() {
    let timer;
    onBeforeUnmount(() => {
      if (timer !== void 0) {
        clearTimeout(timer);
      }
    });

    return {
      leftDrawerOpen: ref(false),
    };
  },
  created() {
    this.getUser();
  },
  methods: {
    selectActive(num) {
      this.active = num;
    },
    viewTopMessage(message, icon, iconColor) {
      this.topDialog = true;
      this.topIcon = icon;
      this.topIconColor = iconColor;
      this.topMessage = message;
      this.timer = setTimeout(() => {
        this.topDialog = false;
      }, 5000);
    },
    async getUser() {
      const cognitoUser = await Auth.currentAuthenticatedUser();
      this.user = cognitoUser.getUsername();
    },
  },
};
</script>

<style></style>
