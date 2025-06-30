<template>
  <div>
    <NuxtPage />
  </div>
</template>

<script>
export default {
  setup() {
    const apiKey = '8fe8ba4797cf9dd9f53aad6b4fe11134'
    const url = `https://api.themoviedb.org/3/movie/popular?api_key=${apiKey}&language=en-US&page=1`
    fetch(url)
      .then(res => {
        console.log(res)
        res.json()
      })
      .then(data => {
        console.log(data)
      })
      .catch(error => {
        console.error('Error', error)
      })
  }
}
</script>
