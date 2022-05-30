<template>
  <div id="umamusume-select">
    <q-chip class="glossy" color="orange" text-color="white" icon="star"
      >育成ウマ娘</q-chip
    >
    <select-character
      v-model="setName"
      v-model:modelOptions="names"
      label="名前"
      id="name-select"
    />
    <select-character
      v-model="setAnotherName"
      v-model:modelOptions="anotherNames"
      label="別名"
      id="anothername-select"
    />
    <div class="q-gutter-xs row">
      <select-character
        v-model="setUnique"
        v-model:modelOptions="uniques"
        label="固有スキル"
        id="unique-select"
        style="width: 50vw"
      />
      <select-character
        v-model="umaStatus.uniqueLv"
        v-model:modelOptions="uniqueLvList"
        label="スキルLv"
        id="uniquelv-select"
        style="width: 26vw"
      />
    </div>
  </div>
  <div id="param-input">
    <q-chip class="glossy" color="orange" text-color="white" icon="star"
      >パラメータ</q-chip
    >
    <div class="q-gutter-xs row">
      <input-param
        v-model="umaStatus.speed"
        label="スピード"
        style="width: 15vw"
      />
      <input-param
        v-model="umaStatus.stamina"
        label="スタミナ"
        style="width: 15vw"
      />
      <input-param
        v-model="umaStatus.power"
        label="パワー"
        style="width: 15vw"
      />
      <input-param v-model="umaStatus.guts" label="根性" style="width: 15vw" />
      <input-param
        v-model="umaStatus.intelligence"
        label="賢さ"
        style="width: 15vw"
      />
    </div>
  </div>
  <div id="aptitude-select">
    <q-chip class="glossy" color="orange" text-color="white" icon="star"
      >適性</q-chip
    >
    <div class="q-gutter-xs row">
      <q-select
        outlined
        v-model="umaStatus.turf"
        :options="paramAlphabet"
        label="芝"
        id="turf-select"
        style="width: 20vw"
      />
      <q-select
        outlined
        v-model="umaStatus.dirt"
        :options="paramAlphabet"
        label="ダート"
        id="dirt-select"
        style="width: 20vw"
      />
    </div>
    <div class="q-gutter-xs row">
      <q-select
        outlined
        v-model="umaStatus.short"
        :options="paramAlphabet"
        label="短距離"
        id="short-select"
        style="width: 20vw"
      />
      <q-select
        outlined
        v-model="umaStatus.mile"
        :options="paramAlphabet"
        label="マイル"
        id="mile-select"
        style="width: 20vw"
      />
      <q-select
        outlined
        v-model="umaStatus.medium"
        :options="paramAlphabet"
        label="中距離"
        id="medium-select"
        style="width: 20vw"
      />
      <q-select
        outlined
        v-model="umaStatus.long"
        :options="paramAlphabet"
        label="長距離"
        id="long-select"
        style="width: 20vw"
      />
    </div>
    <div class="q-gutter-xs row">
      <q-select
        outlined
        v-model="umaStatus.runner"
        :options="paramAlphabet"
        label="逃げ"
        id="runner-select"
        style="width: 20vw"
      />
      <q-select
        outlined
        v-model="umaStatus.leader"
        :options="paramAlphabet"
        label="先行"
        id="leader-select"
        style="width: 20vw"
      />
      <q-select
        outlined
        v-model="umaStatus.betweener"
        :options="paramAlphabet"
        label="差し"
        id="betweener-select"
        style="width: 20vw"
      />
      <q-select
        outlined
        v-model="umaStatus.chaser"
        :options="paramAlphabet"
        label="追込"
        id="chaser-select"
        style="width: 20vw"
      />
    </div>
  </div>
</template>

<script>
import umamusumeJson from "../assets/umamusumeList.json";
import inputParam from "./InputParam.vue";
import selectCharacter from "./SelectCharacter.vue";

export default {
  name: "field-param",
  props: ["modelObject"],
  components: {
    inputParam,
    selectCharacter,
  },
  data() {
    return {
      umaStatus: [],

      /* Selectの表示内容 */
      umamusumes: umamusumeJson.List,
      names: [""],
      anotherNames: [""],
      uniques: [""],
      uniqueLvList: [1, 2, 3, 4, 5, 6],
      paramAlphabet: ["S", "A", "B", "C", "D", "E", "F", "G"],
    };
  },
  created() {
    this.setList();
  },
  computed: {
    setName: {
      get() {
        return this.umaStatus.name;
      },
      set(value) {
        this.anotherNames = [
          ...new Set(
            this.umamusumes
              .filter((el) => el.name === value)
              .map((el2) => el2.anotherName)
          ),
        ];
        this.setSelectValue(value);
      },
    },
    setAnotherName: {
      get() {
        return this.umaStatus.anotherName;
      },
      set(value) {
        let dataList = [
          ...new Set(
            this.umamusumes
              .filter((el) => el.name === this.umaStatus.name)
              .filter((el2) => el2.anotherName === value)
          ),
        ][0];
        this.uniques =
          dataList.unique === dataList.unique2
            ? [dataList.unique]
            : [dataList.unique, dataList.unique2];
        this.setSelectValue(
          this.umaStatus.name,
          value,
          this.uniques[0],
          "",
          dataList.turf,
          dataList.dirt,
          dataList.short,
          dataList.mile,
          dataList.medium,
          dataList.long,
          dataList.runner,
          dataList.leader,
          dataList.betweener,
          dataList.chaser
        );
      },
    },
    setUnique: {
      get() {
        return this.umaStatus.unique;
      },
      set(value) {
        this.umaStatus.unique = value;
        // スキル内に同名の固有スキルがあったら削除
        const index = this.umaStatus.skills.indexOf(value);
        if (index >= 0) this.umaStatus.skills.splice(index, 1);
      },
    },
  },
  methods: {
    // 初期処理
    setList() {
      this.names = [...new Set(this.umamusumes.map((el) => el.name))];
    },
    // スキル選択画面で選択した項目を設定
    setSkills(value) {
      const index = this.skills.indexOf(value);
      index >= 0 ? this.skills.splice(index, 1) : this.skills.push(value);
    },
    // ウマ娘の名称から連動する要素の表示を設定する
    setSelectValue(
      name = "",
      anotherName = "",
      unique = "",
      uniqueLv = "",
      turf = "",
      dirt = "",
      short = "",
      mile = "",
      medium = "",
      long = "",
      runner = "",
      leader = "",
      betweener = "",
      chaser = ""
    ) {
      this.umaStatus.name = name;
      this.umaStatus.anotherName = anotherName;
      this.umaStatus.unique = unique;
      this.umaStatus.uniqueLv = uniqueLv;
      this.umaStatus.turf = turf;
      this.umaStatus.dirt = dirt;
      this.umaStatus.short = short;
      this.umaStatus.mile = mile;
      this.umaStatus.medium = medium;
      this.umaStatus.long = long;
      this.umaStatus.runner = runner;
      this.umaStatus.leader = leader;
      this.umaStatus.betweener = betweener;
      this.umaStatus.chaser = chaser;
    },
  },
  watch: {
    modelObject: {
      immediate: true,
      handler(value) {
        this.umaStatus = value;
      },
    },
  },
};
</script>
