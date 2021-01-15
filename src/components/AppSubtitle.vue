<template>
  <h2>{{ block.value }}</h2>
  <app-button :text="'Удалить'" :class="'danger'" @click="$emit('remove', block.id)" ></app-button>
</template>

<script>
import AppButton from '@/components/AppButton'
export default {
  emits: ['remove'],
  props: {
    block: {
      type: Object,
      required: true,
      validator () {
        return true
      }
    }
  },
  components: { AppButton }
}
</script>

<style scoped>

</style>
