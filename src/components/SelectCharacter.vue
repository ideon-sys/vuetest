<template>
  <q-select        
    outlined
    v-model="modelValue"
    :options="modelOptions"
    style="width: 80vw"
  />
</template>

<script>
  export default {
    name: 'selectCharacter',
    props: ['modelOptions'],
    data(){
      return{
        modelValue: ''
      }
    }
  }
</script>
