<template>
  <div>
    <div v-for="movie in data" v-bind:key="movie.id">
      <Card :movie="movie" />
    </div>
  </div>
</template>

<script>
import JsonApi from 'devour-client'
import Card from '~/components/Card.vue'

export default {
  components: {
    Card
  },
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
    const { data } = await jsonApi.findAll('movies', {
      include: 'actors,genre,director'
    })
    return { data }
  }
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 80vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
