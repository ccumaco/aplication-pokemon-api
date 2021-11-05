<template>
  <div class="container-search-pokemon">
    <div v-if="loading" class="container-loading">
      <img
        class="loading"
        src="../assets/images/Loader-pokemon.svg"
        alt="loading pokebola"
      />
    </div>
    <div class="targets-pokemons">
      <div class="container-input">
        <input
          class="input-search"
          @click="openDialog({}, false)"
          type="text"
          name="seach"
          id="seach"
          v-model="textSearch"
          placeholder="Search"
        />
      </div>
      <div class="card-search" v-if="filters.length > 0">
        <p
          class="input-search target"
          @click="openDialog(pokemon, true)"
          v-for="(pokemon, index) in filters"
          :key="index"
        >
          {{ pokemon.name }}
          <img
            v-if="!pokemon.visible"
            @click="addOrDeleteFavorite(pokemon)"
            class="star"
            src="../assets/images/star-disable.png"
            alt="star"
          />
          <img
            v-else
            class="star"
            @click="addOrDeleteFavorite(pokemon)"
            src="../assets/images/star-active.png"
            alt="star"
          />
        </p>
      </div>
      <div class="not-found" v-if="notFound">
        <h4 class="title-not-found">Uh-oh!</h4>
        <p class="paragraph-not-found">You look lost on your journey!</p>
        <button class="come-back" @click="textSearch = ''">Go back home</button>
      </div>
      <div
        class="card-search"
        v-if="!filters.length && !notFound && !stepFavorites"
      >
        <p
          class="input-search target"
          @click="openDialog(pokemon, !showDialog)"
          v-for="(pokemon, index) in pokemons"
          :key="index"
        >
          {{ pokemon.name }}
          <img
            v-if="!pokemon.visible"
            @click="addOrDeleteFavorite(pokemon)"
            class="star"
            src="../assets/images/star-disable.png"
            alt="star"
          />
          <img
            v-else
            class="star"
            @click="addOrDeleteFavorite(pokemon)"
            src="../assets/images/star-active.png"
            alt="star"
          />
        </p>
      </div>
      <div class="not-found" v-if="!favorites.length && stepFavorites">
        <h4 class="title-not-found">Uh-oh!</h4>
        <p class="paragraph-not-found">You look lost on your journey!</p>
        <button class="come-back" @click="watchAllPokemons()">
          Go back home
        </button>
      </div>
      <div class="card-search" v-if="favorites.length > 0 && stepFavorites">
        <p
          class="input-search target"
          @click="openDialog(pokemon, true)"
          v-for="(pokemon, index) in favorites"
          :key="index"
        >
          {{ pokemon.name }}
          <img
            v-if="!pokemon.visible"
            @click="addOrDeleteFavorite(pokemon)"
            class="star"
            src="../assets/images/star-disable.png"
            alt="star"
          />
          <img
            v-else
            class="star"
            @click="addOrDeleteFavorite(pokemon)"
            src="../assets/images/star-active.png"
            alt="star"
          />
        </p>
      </div>
    </div>
    <div class="options">
      <div class="container-options">
        <button
          class="buttons-options active"
          ref="button1"
          @click="watchAllPokemons()"
        >
          <img src="../assets/images/icon-all.svg" alt="icon all" />
          <span> All </span>
        </button>
        <button class="buttons-options" ref="button2" @click="lookFavorites()">
          <img src="../assets/images/icon-favorites.svg" alt="icon favorites" />
          <span> Favorites </span>
        </button>
      </div>
    </div>
    <Dialog
      :pokemonDialog="pokemonDialog"
      :showDialog="showDialog"
      @setDialog="openDialog"
      @addOrDeleteFavorite="addOrDeleteFavorite"
    />
  </div>
</template>
<script>
import axios from "axios";
import Dialog from "./../components/Dialog.vue";

export default {
  name: "SearchPokemon",

  data() {
    return {
      pokemons: [],
      urlApi: this.$store.state.urlApi,
      loading: true,
      textSearch: "",
      filters: [],
      notFound: false,
      favorites: [],
      stepFavorites: false,
      pokemonDialog: {},
      showDialog: false,
    };
  },
  components: {
    Dialog,
  },
  watch: {
    textSearch() {
      this.findPokemon();
    },
    
  },
  methods: {
    setLoading() {
      this.loading = true;
    },
    openDialog(pokemon, showDialog) {
      this.pokemonDialog = pokemon;
      this.showDialog = showDialog;
    },
    addOrDeleteFavorite(pokemon) {
      if (pokemon.visible) {
        pokemon.visible = false;
        this.$store.state.favorites.splice(
          this.$store.state.favorites.indexOf(pokemon),
          1
        );
      } else {
        pokemon.visible = true;
        this.$store.state.favorites.push(pokemon);
      }
      this.$forceUpdate();
    },
    lookFavorites() {
      this.stepFavorites = true;
      this.favorites = this.$store.state.favorites;
      this.notFound = false;
      this.$refs.button1.className = "buttons-options";
      this.$refs.button2.className += " active";
    },
    watchAllPokemons() {
      this.$refs.button1.className += " active";
      this.$refs.button2.className = "buttons-options";
      this.stepFavorites = false;
    },
    findPokemon() {
      this.filters = this.pokemons.filter((element) => {
        return element.name
          .toLowerCase()
          .includes(this.textSearch.toLowerCase());
      });
      if (this.filters.length === 0) {
        this.notFound = true;
      } else {
        this.notFound = false;
      }
    },
    getPokemons() {
      axios
        .get(`${this.urlApi}/pokemon`)
        .then(
          (response) => (
            (this.pokemons = response.data.results),
            this.pokemons.forEach((element) => {
              if (!element.visible) {
                element.visible = false;
              }
            }),
            setTimeout(() => {
              this.loading = false;
            }, 3000)
          )
        )
        .catch((error) => console.log(error));
    },
  },

  mounted() {
    this.getPokemons();
    setTimeout(() => {}, 1000);
  },
};
</script>
<style lang="scss">
@import "../styles/searchPokemon.scss";
</style>
