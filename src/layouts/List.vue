<template>
  <div class="q-pa-md" style="width: 90vw">
    <div class="row">
      <a>登録済みウマ娘一覧</a>
    </div>
    <q-markup-table>
      <thead>
        <tr>
          <td v-for="column in columns" :key="column">
            {{ column.label }}
            <a
              @click="sort(column)"
              v-if="column.canSort"
              v-text="sortMark"
              :class="[column.descending ? 'roll0' : 'roll180']"
            />
          </td>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in umaStatuses" :key="item.id" @click="viewDialog(item)">
          <td
            v-for="column in columns"
            :key="column"
            v-text="getUmastatusData(item, column.name)"
          />
        </tr>
      </tbody>
    </q-markup-table>
  </div>
  <q-dialog v-model="dialog">
    <dialog-confirm v-model="argUmaStatus" v-model:noRegist="argFalse" />
  </q-dialog>
</template>

<script>
import { API } from "aws-amplify";
import { listStatuses } from "../graphql/queries";
import dialogConfirm from "../components/DialogConfirm.vue";

export default {
  name: "list-components",
  components: {
    dialogConfirm,
  },
  data() {
    return {
      argUmaStatus: [],
      argFalse: false,
      umaStatuses: [],
      errors: [],
      sortMark: "▲",
      sortBy: "名前",
      dialog: false,
      columns: [
        {
          name: "createdAt",
          label: "登録日",
          canSort: true,
          descending: false,
        },
        { name: "name", label: "名前", canSort: true, descending: false },
        { name: "anotherName", label: "", canSort: false },
      ],
    };
  },
  created() {
    this.getumaStatuses();
  },
  methods: {
    async getumaStatuses() {
      const umaStatuses = await API.graphql({
        query: listStatuses,
      });
      this.umaStatuses = umaStatuses.data.listStatuses.items;
    },
    sort(column) {
      console.log("start sort");
      console.log(column);
      this.sortMethod(column.name, column.descending);
      column.descending = !column.descending;
    },
    sortMethod(sortBy, descending) {
      const data = [...this.umaStatuses];
      if (sortBy) {
        data.sort((a, b) => {
          const x = descending ? b : a;
          const y = descending ? a : b;
          if (!["name", "anotherName"].indexOf(sortBy)) {
            // 文字列
            return x[sortBy] > y[sortBy] ? 1 : x[sortBy] < y[sortBy] ? -1 : 0;
          } else if (sortBy === "createdAt") {
            // 日付
            const xdate = new Date(x[sortBy]);
            const ydate = new Date(y[sortBy]);
            return xdate - ydate;
          } else {
            // 数値
            return parseFloat(x[sortBy]) - parseFloat(y[sortBy]);
          }
        });
      }
      this.umaStatuses = data;
    },
    getUmastatusData(item, key) {
      return key === "createdAt"
        ? item[key]//.substring(0, 10) + " " + item[key].substring(11, 19)
        : item[key];
    },
    viewDialog(umaStatus) {
      this.argUmaStatus = umaStatus;
      this.dialog = true;
    }
  },
};
</script>

<style>
.roll0 {
  display: inline-block;
  animation: rollkAnime .1s;
  transform: rotate(0deg);
}
@keyframes rollkAnime {
  0% {
    transform: rotate(180deg);
  }
  50% {
    transform: rotate(270deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
.roll180 {
  display: inline-block;
  animation: rollkAnime2 .1s;
  transform: rotate(180deg);
}
@keyframes rollkAnime2 {
  0% {
    transform: rotate(0deg);
  }
  50% {
    transform: rotate(90deg);
  }
  100% {
    transform: rotate(180deg);
  }
}
</style>