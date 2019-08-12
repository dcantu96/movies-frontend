<template>
  <a-card :title="movie.name">
    <p>{{ movie.genre }}</p>
  </a-card>
</template>

<script>
export default {
  data() {
    return { movie: [] }
  }
}
</script>
