<template>
  <a-form :form="form" theme="dark" @submit="update">
    <a-form-item label="Actor Name">
      <a-input
        v-decorator="[
          'name',
          { initialValue: data.name },
          { rules: [{ required: true, message: 'Please select home team!' }] }
        ]"
        type="text"
        placeholder="Name"
      >
        <a-icon slot="prefix" type="user" style="color: rgba(0,0,0,.25)" />
      </a-input>
    </a-form-item>
    <a-form-item>
      <a-button type="primary" html-type="submit">
        Update
      </a-button>
    </a-form-item>
  </a-form>
</template>

<script>
import JsonApi from 'devour-client'

export default {
  async asyncData({ $axios, route, params, $auth }) {
    const jsonApi = new JsonApi({ apiUrl: $axios.defaults.baseURL })
    jsonApi.define('movie', {
      name: '',
      duration: '',
      actors: {
        jsonApi: 'hasOne',
        type: 'actors'
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
      movieActors: {
        jsonApi: 'hasMany',
        type: 'movieActors'
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
    const { data } = await jsonApi.find('actor', route.params.id)
    return { data }
  },
  beforeCreate() {
    this.form = this.$form.createForm(this)
  },
  methods: {
    async update() {
      try {
        const jsonApi = new JsonApi({ apiUrl: this.$axios.defaults.baseURL })
        jsonApi.define('movie', {
          name: '',
          duration: '',
          actors: {
            jsonApi: 'hasOne',
            type: 'actors'
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
          movieActors: {
            jsonApi: 'hasMany',
            type: 'movieActors'
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
        await jsonApi.update('actor', {
          id: this.data.id,
          name: this.form.getFieldValue('name')
        })
        this.$toast.success('Actor updated successfully!')
      } catch (e) {
        this.$toast.error('Error while updating actor!')
      }
    }
  }
}
</script>
