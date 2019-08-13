<template>
  <div>
    <VueFuse
      placeholder="Search for directors"
      event-name="results"
      :list="data"
      :keys="['name']"
      style="width: 200px; marginBottom: 20px;"
    />
    <n-link to="/directors/new">
      <a-button type="primary">
        New director
      </a-button>
    </n-link>
    <div class="grid">
      <div v-for="director in results" :key="director.id">
        <a-card :title="director.name">
          <h3>Movies:</h3>
          <p v-for="movie in director.movies" :key="movie.id">
            {{ movie.name }}
          </p>
        </a-card>
      </div>
    </div>
  </div>
</template>

<script>
import JsonApi from 'devour-client'

export default {
  data() {
    return {
      results: []
    }
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
    const { data } = await jsonApi.findAll('directors', {
      include: 'movies'
    })
    return { data }
  },
  created() {
    this.$on('results', (results) => {
      this.results = results
    })
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

.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  grid-auto-rows: auto;
  grid-gap: 3rem;
}
</style>
