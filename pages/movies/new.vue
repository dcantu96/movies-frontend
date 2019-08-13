<template>
  <a-form :form="form" theme="dark" @submit.prevent="create">
    <a-form-item label="Movie Name">
      <a-input v-decorator="['name']" type="text" placeholder="Name">
        <a-icon slot="prefix" type="user" style="color: rgba(0,0,0,.25)" />
      </a-input>
    </a-form-item>
    <a-form-item label="Director">
      <a-select
        v-decorator="[
          'director',
          { rules: [{ required: true, message: 'Please select director!' }] }
        ]"
        placeholder="Please select a director"
      >
        <a-select-option v-for="director in directors" :key="director.id">
          {{ director.name }}
        </a-select-option>
      </a-select>
    </a-form-item>
    <a-form-item label="Genre">
      <a-select
        v-decorator="[
          'genre',
          { rules: [{ required: true, message: 'Please select a genre!' }] }
        ]"
        placeholder="Please select a genre"
      >
        <a-select-option v-for="genre in genres" :key="genre.id">
          {{ genre.name }}
        </a-select-option>
      </a-select>
    </a-form-item>
    <a-form-item label="Actor 1">
      <a-select
        v-decorator="[
          'actor-1',
          { rules: [{ required: true, message: 'Please select actor!' }] }
        ]"
        placeholder="Please select a actor"
      >
        <a-select-option v-for="actor in actors" :key="actor.id">
          {{ actor.name }}
        </a-select-option>
      </a-select>
    </a-form-item>
    <a-form-item label="Actor 2">
      <a-select
        v-decorator="[
          'actor-2',
          { rules: [{ required: true, message: 'Please select actor!' }] }
        ]"
        placeholder="Please select a actor"
      >
        <a-select-option v-for="actor in actors" :key="actor.id">
          {{ actor.name }}
        </a-select-option>
      </a-select>
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
  async asyncData({ $axios }) {
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
    const directors = await jsonApi.findAll('director')
    const actors = await jsonApi.findAll('actor')
    const genres = await jsonApi.findAll('genre')
    return {
      directors: directors.data,
      actors: actors.data,
      genres: genres.data
    }
  },
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
        await jsonApi.create('movie', {
          name: this.form.getFieldValue('name'),
          duration: this.form.getFieldValue('duration'),
          director: {
            id: this.form.getFieldValue('director')
          },
          genre: {
            id: this.form.getFieldValue('genre')
          },
          actors: [
            { id: this.form.getFieldValue('actor-1') },
            { id: this.form.getFieldValue('actor-2') }
          ]
        })
        this.$toast.success('Movie created successfully!')
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
