<template>
  <q-card>
    <q-tabs
      v-model="slide"
      dense
      class="text-grey"
      active-color="primary"
      indicator-color="primary"
      align="justify"
      narrow-indicator
    >
      <q-tab name="param" label="パラメータ" />
      <q-tab name="skill" label="スキル" />
      <q-btn
        class="self-end"
        id="confirm"
        push
        :color="!canConfirm ? 'grey-5' : 'teal'"
        :text-color="!canConfirm ? 'grey-8' : 'white'"
        :disable="!canConfirm"
        @click="this.dialog = true"
      >
        <q-icon name="create" />

        <div>{{ user }}</div>
      </q-btn>
    </q-tabs>

    <q-separator />

    <q-tab-panels v-model="slide" animated>
      <q-tab-panel name="param">
        <field-param v-model:modelObject="umaStatus" />
      </q-tab-panel>

      <q-tab-panel name="skill">
        <field-skill v-model="umaStatus" @clickEvent="setSkills" />
      </q-tab-panel>
    </q-tab-panels>
  </q-card>

  <q-separator dark />
  <q-dialog v-model="dialog">
    <dialog-confirm v-model="umaStatus" @clickEvent="completedEvent" />
  </q-dialog>
</template>

<script>
import { ref } from "vue";
import umaStatusArrayImport from "../assets/status";
import fieldSkill from "../components/FieldSkill.vue";
import fieldParam from "../components/FieldParam.vue";
import dialogConfirm from "../components/DialogConfirm.vue";

export default {
  name: "modal-body",
  props: ["modelValue"],
  components: {
    fieldSkill,
    fieldParam,
    dialogConfirm,
  },
  data() {
    return {
      umaStatus: umaStatusArrayImport.umamusumeStatus(),
      canConfirm: true,
      dialog: false,
      slide: ref("param"),
      user: "",
    };
  },
  created() {
    this.resetUmaStatus();
  },
  setup() {
    return {
      initialPagination: {
        sortBy: "desc",
        descending: false,
        page: 1,
        rowsPerPage: 19,
      },
    };
  },
  methods: {
    setSkills(value) {
      if (value === this.umaStatus.unique) return; // 固有スキルとの重複は無視する
      const index = this.umaStatus.skills.indexOf(value);
      if (index >= 0) {
        this.umaStatus.skills.splice(index, 1);
      } else {
        this.umaStatus.skills.push(value);
      }
    },
    completedEvent(value) {
      // 登録後の後処理
      this.dialog = false;
      if (value) {
        // 登録成功
        this.resetUmaStatus();
        this.setResultDialog("登録に成功しました。", "done", "teal");
      } else {
        // 登録失敗
        this.setResultDialog(
          "登録に失敗しました。再度、時間を置いて実行してください。",
          "dangerous",
          "red"
        );
      }
    },
    setResultDialog(message, icon, iconColor) {
      this.$emit("setTopMessage", message, icon, iconColor);
    },
    resetUmaStatus() {
      this.umaStatus = umaStatusArrayImport.umamusumeStatus();
      this.umaStatus.user = this.user;
    },
  },
  watch: {
    umaStatus: {
      handler(value) {
        // Varidate
        this.canConfirm = true;
        Object.keys(value).forEach((key) => {
          if (typeof value[key] === "number") {
            if (value[key] < 0 || value[key] > 1200) this.canConfirm = false;
          }
        });
      },
      deep: true,
    },
    modelValue: {
      immediate: true,
      handler(value) {
        this.user = value;
      },
    },
  },
};
</script>

<style></style>
