<template>
  <button class="btn" :class="color" @click="$emit('action')" :disabled="isDisabled">{{ text }}</button>
</template>

<script>
export default {
  emits: {
    action: null
  },
  props: {
    isDisabled: {
      type: Boolean
    },
    text: {
      type: String,
      required: true,
      validator (value) {
        return !!value.length
      }
    },
    color: {
      type: String,
      default: 'primary',
      validator (value) {
        return ['', 'primary', 'danger'].includes(value)
      }
    }
  }

}
</script>

<style scoped>

</style>
