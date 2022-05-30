<template>
  <q-input 
    ref="inputRef"
    outlined 
    v-model.number="modelValue" 
    type="number"
    :rules="[ val => Number(val) <= 1200 || '0〜1200の数値を入力してください。']"
  />
</template>

<script>
  import { ref } from 'vue'

  export default {
    name: 'input-param',
    setup () {
      const inputRef = ref(null)
      return {
        modelValue: ref(''),
        inputRef,
        reset () {
          inputRef.value.resetValidation()
        }
      }
    },
  }
</script>
