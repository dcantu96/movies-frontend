<template>
  <a-form :form="form" theme="dark" @submit.prevent="create">
    <a-form-item label="Actor Name">
      <a-input v-decorator="['name']" type="text" placeholder="Name">
        <a-icon slot="prefix" type="user" style="color: rgba(0,0,0,.25)" />
      </a-input>
    </a-form-item>
    <a-form-item>
      <a-button type="primary" html-type="submit">
        Create
      </a-button>
    </a-form-item>
  </a-form>
</template>

<script>
import JsonApi from 'devour-client'

export default {
  beforeCreate() {
    this.form = this.$form.createForm(this)
  },
  methods: {
    async create() {
      try {
        const jsonApi = new JsonApi({ apiUrl: this.$axios.defaults.baseURL })
        jsonApi.define('movie', {
          name: '',
          duration: '',
          actors: {
            jsonApi: 'hasMany',
            type: 'actors'
          },
          'movie-actors': {
            jsonApi: 'hasMany',
            type: 'movie-actors'
          },
          genre: {
            jsonApi: 'hasOne',
            type: 'genres'
          },
          director: {
            jsonApi: 'hasOne',
            type: 'directors'
          }
        })
        jsonApi.define('actor', {
          name: '',
          'movie-actors': {
            jsonApi: 'hasMany',
            type: 'movie-actors'
          },
          movies: {
            jsonApi: 'hasMany',
            type: 'movies'
          }
        })
        jsonApi.define('genre', {
          name: '',
          movies: {
            jsonApi: 'hasMany',
            type: 'genres'
          }
        })
        jsonApi.define('director', {
          name: '',
          movies: {
            jsonApi: 'hasMany',
            type: 'directors'
          }
        })
        await jsonApi.create('actor', {
          name: this.form.getFieldValue('name')
        })
        this.$toast.success('Actor created successfully!')
      } catch (e) {
        this.$toast.error(e)
      }
    }
  }
}
</script>
<style>
.button-add {
  margin-bottom: 10px;
}
</style>
