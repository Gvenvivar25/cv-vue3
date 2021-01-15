<template>
  <div class="card">
    <h2>Комментарии</h2>
    <ul class="list">
      <li class="list-item" v-for="comment in commentsList" :key="comment.id">
        <div>
          <p><strong>{{ comment.email }}</strong></p>
          <small>{{ comment.body }}</small>
        </div>
      </li>
    </ul>
  </div>

</template>

<script>
export default {
  props: {
    commentsList: {
      type: Object
    }
  }

}
</script>

<style scoped>

</style>
