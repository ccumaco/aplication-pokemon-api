<template>
  <div class="background-dialog" v-if="dataPokemon.name && showDialog">
    <div class="card-dialog">
      <div class="background-pokemon">
        <span class="close-btn" @click="updateDialogParent(dataPokemon, false)">X</span>
        <img
        class="image-pokemon"
          :src="dataPokemon.sprites.other.dream_world.front_default"
          :alt="`Imagen del el pokemon ${dataPokemon.name}`"
        />
      </div>
      <p class="options-character"><b>Name:</b> {{ dataPokemon.name }}</p>
      <p class="options-character"><b>Weight:</b> {{ dataPokemon.weight }}</p>
      <p class="options-character"><b>Types:</b> {{ dataPokemon.types[0].type.name }}</p>
      <input id="copyText" :value="stringData" />
      <div class="container-share">
        <button class="share" @click="sharePokemon(dataPokemon)">Share to my friends</button>
          <img
            v-if="!pokemonDialog.visible"
            class="star"
            src="../assets/images/star-disable.png"
            alt="star"
            @click="$emit('addOrDeleteFavorite', pokemonDialog)"
          />
          <img
          @click="$emit('addOrDeleteFavorite', pokemonDialog)"
            v-else
            class="star"
            src="../assets/images/star-active.png"
            alt="star"
          />
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";

export default {
  name: "Dialog",
  data() {
    return {
      urlApi: this.$store.state.urlApi,
      dataPokemon: {},
      stringData: []
    };
  },
    computed:{
  },
  props: ["pokemonDialog", "showDialog"],
  methods: {
    updateDialogParent(dataPokemon, visible) {
      this.$emit("setDialog", dataPokemon, visible);
    },
    sharePokemon(){
      this.stringData = []
        if (typeof this.stringData != 'string') {
          
            for (let i = 0; i < document.querySelectorAll('.options-character').length; i++) {
              const element = document.querySelectorAll('.options-character')[i];
            this.stringData.push(element.innerText);
          }
        }
          this.stringData = this.stringData.toString()
          document.getElementById('copyText').focus();
          document.execCommand('selectAll');
          document.execCommand('copy');
    },
    getPokemons() {
      axios
        .get(`${this.urlApi}/pokemon/${this.pokemonDialog.name}`)
        .then(
          (response) => (
            (this.dataPokemon = response.data)
          )
        )
        .catch((error) => console.log(error));
    },
  },
  watch: {
    showDialog() {
      this.getPokemons();
    },
    
  },
};
</script>
<style lang="scss">

@import "../styles/Dialog.scss";

</style>