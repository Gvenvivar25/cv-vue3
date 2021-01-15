<template>
  <div class="container column">
    <form class="card card-w30" @submit.prevent="createBlock">
      <div class="form-control">
        <label for="type">Тип блока</label>
        <select id="type" :value="type" v-model="type">
          <option value="title">Заголовок</option>
          <option value="subtitle">Подзаголовок</option>
          <option value="avatar">Аватар</option>
          <option value="text">Текст</option>
        </select>
      </div>

      <div class="form-control">
        <label for="value">Значение</label>
        <textarea id="value" rows="8" v-model="text"></textarea>
      </div>

      <app-button :text="'Добавить'" :is-disabled="isDisabled"></app-button>
    </form>

    <div class="card card-w70">
      <app-alert :alert="alert"></app-alert>
      <template v-if="blocks.length">
        <component v-for="block in blocks" :key="block.id" :is="'app-' + block.type" :block="block" @remove="removeBlock"></component>
      </template>
      <h3 v-else>Добавьте первый блок, чтобы увидеть результат</h3>
    </div>
  </div>
  <div class="container">
    <p>
      <app-button :text="'Загрузить комментарии'" @action="loadComments" ></app-button>
    </p>
    <app-loader v-if="loading"></app-loader>
    <app-comments-list v-if="commentsList.length !== 0" :comments-list="commentsList"></app-comments-list>
  </div>
</template>

<script>
import AppButton from '@/components/AppButton'
import AppAvatar from '@/components/AppAvatar'
import AppCommentsList from '@/components/AppCommentsList'
import AppSubtitle from '@/components/AppSubtitle'
import AppText from '@/components/AppText'
import AppTitle from '@/components/AppTitle'
import AppLoader from '@/components/AppLoader'
import AppAlert from '@/components/AppAlert'

export default {
  data () {
    return {
      type: 'title',
      text: '',
      commentsList: [],
      loading: false,
      alert: null,
      blocks: []
    }
  },
  mounted () {
    this.loadBlocks()
  },

  methods: {
    addBlock () {
      this.blocks.push({
        id: Math.random(),
        type: this.type,
        value: this.text
      })
      this.text = ''
      this.showAlert('Блок успешно добавлен!', 'primary')
    },
    showAlert (title, type, text = '') {
      this.alert = {
        title,
        text,
        type
      }
      setTimeout(() => {
        this.alert = null
      }, 3000)
    },

    async createBlock () {
      const url = 'https://cv-vue3-default-rtdb.firebaseio.com/blocks.json'
      const response = await fetch(url, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          type: this.type,
          value: this.text
        })
      })

      const firebaseData = await response.json()
      this.blocks.push({
        type: this.type,
        value: this.text,
        id: firebaseData.name
      })
      this.text = ''
      this.showAlert('Блок успешно добавлен!', 'primary')
    },

    async removeBlock (id) {
      try {
        await fetch(`https://cv-vue3-default-rtdb.firebaseio.com/blocks/${id}.json`, {
          method: 'DELETE'
        })
        this.blocks = this.blocks.filter(block => block.id !== id
        )
      } catch (e) {
        this.showAlert('Что-то пошло не так...', 'danger', e.message)
      }

      this.showAlert('Блок успешно удален!', 'primary')
    },

    async loadBlocks () {
      const url = 'https://cv-vue3-default-rtdb.firebaseio.com/blocks.json'
      this.loading = true
      try {
        const response = await fetch(url)
          .then(response => response.json())
        this.blocks = Object.keys(response).map(key => {
          return {
            id: key,
            ...response[key]
          }
        })
        this.loading = false
        this.showAlert('Блоки успешно загружены!', 'primary')
      } catch (e) {
        this.showAlert('Ошибка!', 'danger', e.message)
      }
    },

    loadComments () {
      this.loading = true
      setTimeout(async () => {
        try {
          const response = await fetch('https://jsonplaceholder.typicode.com/comments?_limit=42')
            .then(response => response.json())
          this.commentsList = Object.keys(response).map(key => {
            return {
              id: key,
              ...response[key]
            }
          })
          this.loading = false
          this.showAlert('Комментарии успешно загружены!', 'primary')
        } catch (e) {
          this.showAlert('Ошибка!', 'danger', e.message)
        }
      }, 300)
    }

  },
  computed: {
    isDisabled () {
      return this.text.length < 4
    }

  },

  components: {
    AppTitle,
    AppSubtitle,
    AppText,
    AppAvatar,
    AppButton,
    AppCommentsList,
    AppLoader,
    AppAlert
  }

}
</script>

<style>
  .avatar {
    display: flex;
    justify-content: center;
  }

  .avatar img {
    width: 200px;
    height: 200px;
    border-radius: 30%;
  }
</style>
