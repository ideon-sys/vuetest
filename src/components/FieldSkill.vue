<template>
  <div class="q-gutter-md row">
    <q-select
      Rounded
      outlined
      bg-color="white"
      label="レアリティ"
      v-model="filterRarity"
      :options="options"
      style="height: 10vh; width: 30vw"
    />
    <q-select
      Rounded
      outlined
      bg-color="white"
      label="タイプ"
      v-model="filterType"
      :options="options2"
      style="height: 10vh; width: 30vw"
    />
  </div>
  <q-card-section id="skill-select" class="q-gutter-xs row">
    <div>
      <q-chip class="glossy" color="orange" text-color="white" icon="star">
        スキル一覧
      </q-chip>
      <q-layout
        view="lHh Lpr lFf"
        container
        style="height: 50vh; width: 40vw"
        class="shadow-2 rounded-borders"
      >
        <a v-for="item in setList()" :key="item.name">
          <q-btn text-color="black" @click="clickButton(item.name)">
            <q-icon name="label" :style="loadColor(item.type)" />
            <a style="font-size: 0.8vw">{{ item.name }}</a>
          </q-btn>
        </a>
      </q-layout>
    </div>
    <div>
      <q-chip class="glossy" color="pink" text-color="white" icon="check">
        選択中スキル
      </q-chip>
      <q-layout
        view="lHh Lpr lFf"
        container
        style="height: 50vh; width: 40vw"
        class="shadow-2 rounded-borders"
      >
        <a v-for="item in umaStatus.skills" :key="item">
          <q-btn text-color="black" @click="clickButton(item)">
            <q-icon name="label" :style="loadColor(item)" />
            <a style="font-size: 0.8vw">{{ item }}</a>
          </q-btn>
        </a>
      </q-layout>
    </div>
  </q-card-section>
</template>

<script>
import skillJson from "../assets/skillList.json";

export default {
  name: "field-skill",
  props: ["modelValue"],
  data() {
    return {
      skillJson: skillJson.skillData,
      filterRarity: ["全て"],
      filterType: ["全て"],
      umaStatus: [],
      options: [
        { label: "ノーマル", val: "1" },
        { label: "レア", val: "2" },
        { label: "固有", val: "3" },
        { label: "全て", val: "0" },
      ],
      options2: [
        { label: "バフ", val: "0" },
        { label: "デバフ", val: "1" },
        { label: "フィールド", val: "2" },
        { label: "回復", val: "3" },
        { label: "デメリット", val: "4" },
        { label: "アオハル", val: "6" },
        { label: "全て", val: "-1" },
      ],
    };
  },
  methods: {
    setList() {
      let skills = this.skillJson;

      if (this.filterRarity.val == 3) {
        skills = skills.filter((el) => el.rarity >= this.filterRarity.val);
        this.filterType = "全て";
      } else {
        if (this.filterRarity.val != 0 && this.filterRarity.val) {
          skills = skills.filter((el) => el.rarity == this.filterRarity.val);
        }
        if (this.filterType.val >= 0) {
          skills = skills.filter((el) => el.type == this.filterType.val);
        }
      }
      return skills;
    },
    loadColor(value) {
      console.log(isNaN(value))
      let type = isNaN(value)
        ? Number(
            this.skillJson.filter((el) => el.name == value).map((el) => el.type)
          )
        : value;
      switch (type) {
        case 0:
          // バフスキル
          return "color: #FA9A2F;";
        case 1:
          // デバフスキル
          return "color: #F65858;";
        case 2:
          // 回復スキル
          return "color: #17A5F7;";
        case 3:
          // フィールドスキル
          return "color: #8BD43A;";
        case 4:
          // デメリットスキル
          return "color: #ee82ee;";
        case 5:
          // 固有スキル
          return "color: #FA9A2F;";
        case 6:
          // アオハルスキル
          return "color: #FA9A2F;";
        default:
          // 例外
          return "color: #FFFFFF;";
      }
    },
    clickButton(value) {
      this.$emit("clickEvent", value);
    },
  },
  watch: {
    modelValue: {
      immediate: true,
      handler(value) {
        this.umaStatus = value;
      },
    },
  },
};
</script>
