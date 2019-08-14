<template>
  <div>
    <a-input-search
      id="search"
      placeholder="search movies"
      style="width: 200px; marginBottom: 20px;"
      @search="basicSearch"
    />
    <div class="grid">
      <div v-for="movie in data" :key="movie.id">
        <a-card :title="movie.name">
          <p>Genre: {{ movie.genre.name }}</p>
          <p>Duration: {{ movie.duration }}m</p>
        </a-card>
      </div>
    </div>
  </div>
</template>

<script>
import JsonApi from 'devour-client'
// import Card from '~/components/Card.vue'

export default {
  components: {
    // Card
  },
  data() {
    return {
      movies: [],
      searchResults: null
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
      movieActors: {
        jsonApi: 'hasMany',
        type: 'movieActors'
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
  },
  methods: {
    async search(value) {
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
        const { movies } = await jsonApi.findAll(
          'movies',
          {
            filter: {
              name: value
            }
          },
          {
            include: 'actors,genre,director'
          }
        )
        this.data = movies
        this.$toast.success('Search movies successfully!')
      } catch (e) {
        this.$toast.error(e)
      }
    },
    async basicSearch(term) {
      try {
        const options = {
          shouldSort: true,
          threshold: 0.6,
          location: 0,
          distance: 100,
          maxPatternLength: 32,
          minMatchCharLength: 2,
          keys: ['name']
        }
        // const fuse = new Fuse(this.data, options)
        // const result = fuse.search(value)
        await this.$search(term, this.data, options).then((results) => {
          this.data = [...results]
        })
        this.$toast.success('Search movies successfully!')
      } catch (e) {
        this.$toast.error(e)
      }
    }
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
