<template>
  <div id="app">
    <div class="flex justify-center">
      <img alt="Vue logo" src="./assets/logo.png">
    </div>
    <Globals />
    <SearchCountry />
  </div>
</template>

<script>
import Globals from "./components/Globals";
import SearchCountry from "./components/SearchCountry";

export default {
  name: 'App',
  components: {
    Globals, SearchCountry
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Lato:wght@400;700&display=swap');

#app {
  font-family: 'Lato', sans-serif;
  max-width: 850px;
  margin: 0 auto;
}

.flex {
  display: flex;
  width: 100%;
}

.justify-center {
  justify-content: center;
}

img {
  width: 50px;
  height: auto;
}
</style>
