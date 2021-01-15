<template>
  <div class="alert" v-if="alert" :class="alert.type">
    <h3>{{ alert.title }}</h3>
    <p> {{ alert.text }}</p>
  </div>
</template>

<script>
export default {
  props: {
    alert: Object,
    default () {
      return {
        title: '',
        text: '',
        type: ''
      }
    }
  }
}
</script>

<style scoped>

</style>
