<template>
  <q-layout view="Lhh lpR fff" container class="bg-white">
    <q-header class="bg-primary">
      <q-toolbar>
        <q-toolbar-title></q-toolbar-title>
        <q-btn flat v-close-popup round dense icon="close" />
      </q-toolbar>
    </q-header>

    <q-footer class="bg-black text-white" v-if="regist">
      <q-toolbar inset>
        <q-toolbar-title>
          <q-btn flat label="登録" color="white" @click="mutationEvent()" />
        </q-toolbar-title>
      </q-toolbar>
    </q-footer>

    <q-page-container>
      <q-page padding>
        <q-card-section>
          <q-card
            class="my-card text-red"
            style="white-space: pre-wrap; word-wrap: break-word"
          >
            {{ warningMessage }}
            <q-separator size="1vh" />
          </q-card>
          ステータス
          <q-markup-table>
            <tbody>
              <tr v-for="item in createListForUmaStatus()" :key="item">
                <td v-text="item.jpname" />
                <td v-text="item.value" />
              </tr>
            </tbody>
          </q-markup-table>
        </q-card-section>
        <q-card-section class="col-md-4">
          スキル
          <q-markup-table>
            <tbody>
              <tr v-for="item in createListForSkills()" :key="item">
                <td v-text="item.name" />
              </tr>
            </tbody>
          </q-markup-table>
        </q-card-section>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<script>
import { API } from "aws-amplify";
import { Loading, QSpinnerGears } from "quasar";
import { createStatus } from "../graphql/mutations";

export default {
  name: "dialog-confirm",
  props: ["modelValue", "noRegist"],
  data() {
    return {
      umaStatus: [],
      slide: "confirm",

      warningMessage: "",
      canRegist: false,

      isCreateSuccess: true,
      resultBgColor: "teal",
      resultMessage: "登録が完了しました！",
      regist: true,
    };
  },
  methods: {
    createStatus() {
      // test
      console.log("create start");
      if (this.slide === "confirm") {
        return new Promise((resolve, reject) => {
          setTimeout(() => {
            console.log("create end");
            if (this.slide === "confirm") {
              reject(new Error("例外です"));
            } else {
              resolve("OK");
            }
          }, 1000);
        });
      } else {
        try {
          API.graphql({
            query: createStatus,
            variables: { input: this.umaStatus },
          });
          return;
        } catch (err) {
          return err;
        }
      }
    },
    async mutationEvent() {
      Loading.show({
        spinner: QSpinnerGears,
        message: "登録しています...そのまましばらくお待ちください...",
      });
      await this.createStatus().catch(() => {
        this.isCreateSuccess = false;
      });
      Loading.hide();
      this.slide = "completed";
      this.completedConfirm(this.isCreateSuccess);
    },
    createListForUmaStatus() {
      console.log(this.umaStatus);
      return [
        { jpname: "名前", value: this.umaStatus.name },
        { jpname: "別名", value: this.umaStatus.anotherName },
        { jpname: "固有", value: this.umaStatus.unique },
        { jpname: "スキルLv", value: this.umaStatus.uniqueLv },
        { jpname: "スピード", value: this.umaStatus.speed },
        { jpname: "スタミナ", value: this.umaStatus.stamina },
        { jpname: "パワー", value: this.umaStatus.power },
        { jpname: "根性", value: this.umaStatus.guts },
        { jpname: "賢さ", value: this.umaStatus.intelligence },
        { jpname: "芝", value: this.umaStatus.turf },
        { jpname: "ダート", value: this.umaStatus.dirt },
        { jpname: "短距離", value: this.umaStatus.short },
        { jpname: "マイル", value: this.umaStatus.mile },
        { jpname: "中距離", value: this.umaStatus.medium },
        { jpname: "長距離", value: this.umaStatus.long },
        { jpname: "逃げ", value: this.umaStatus.runner },
        { jpname: "先行", value: this.umaStatus.leader },
        { jpname: "差し", value: this.umaStatus.betweener },
        { jpname: "追込", value: this.umaStatus.chaser },
      ];
    },
    createListForSkills() {
      let skills = [];
      this.umaStatus.skills.forEach((item) => skills.push({ name: item }));
      return skills;
    },
    setWarningMessage() {
      let msg = [];
      // パラメータに0の項目がある場合
      const param = [
        { name: "スピード", value: this.umaStatus.speed },
        { name: "スタミナ", value: this.umaStatus.stamina },
        { name: "パワー", value: this.umaStatus.power },
        { name: "根性", value: this.umaStatus.guts },
        { name: "賢さ", value: this.umaStatus.intelligence },
      ];

      let result = param.filter((el) => el.value == 0).map((el) => el.name);
      if (result.length > 0) msg.push(result.join() + "の項目の値が0です。");

      // スキルが登録されていない場合
      if (this.umaStatus.skills.length <= 0)
        msg.push("スキルが登録されていません。");

      this.warningMessage = msg.join("\n");
    },
    completedConfirm(isSuccess) {
      this.$emit("clickEvent", isSuccess);
    },
  },
  watch: {
    modelValue: {
      immediate: true,
      handler(value) {
        this.umaStatus = value;
        this.setWarningMessage();
      },
    },
    noRegist: {
      immediate: true,
      handler(value) {
        this.regist = value;
      },
    }
  },
};
</script>
