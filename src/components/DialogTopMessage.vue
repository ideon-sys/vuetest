<template>
  <!-- メッセージダイアログ -->
  <q-dialog v-model="modelValue" seamless position="top">
    <q-card style="width: 50vw">
      <q-card-section class="row items-center no-wrap">
        <q-icon :name="icon" :color="iconColor" size="lg" />
        <div class="text-weight-bold" v-text="message" />
        <q-space />
        <q-btn flat round icon="close" v-close-popup />
      </q-card-section>
    </q-card>
  </q-dialog>
</template>

<script>

export default {
  name: "dialog-top-message",
  props: ["message", "icon", "iconColor"],
  data() {
    return {};
  },
  
};
</script>
