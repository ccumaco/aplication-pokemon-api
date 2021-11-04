<template>
  <div class="template-home">
      <div v-if="false" class="container-loading">
        <img class="loading" src="../assets/images/Loader-pokemon.svg" alt="loading pokebola">
      </div>
      <div class="container-home">
        <img class="img-pikachu" src="../assets/images/pikachu-home.png" alt="pikachu saludando">
        <h2 class="title-welcome">Welcome to Pokédex</h2>
        <p class="paragraph-welcome">The digital encyclopedia created by Professor Oak is an invaluable tool to Trainers in the Pokémon world.</p>
        <router-link to="/search-pokemon" class="link-start">
            <button class="get-started">Get Started</button>
        </router-link>
    </div>
  </div>
</template>
<script>

export default {
  name: "Home",
  
};
</script>
<style lang="scss">
@import "../styles/home.scss";
</style>