<template>
    <div>
      <PokemonSearch :pokemonList="pokemonList" />
    </div>
  </template>
  
  <script setup lang="ts">
  import { ref } from 'vue';
  import PokemonSearch from '~/components/PokemonSearch.vue';
  
  const pokemonList = ref([]);
  
  const { data, error } = await useAsyncData('pokemonData', () => 
    $fetch('https://pokeapi.co/api/v2/pokemon?limit=100')
  );
  
  if (data.value) {
    pokemonList.value = data.value.results;
    console.log("Liste des Pokémon:", pokemonList.value);
  }
  
  if (error.value) {
    console.error('Erreur lors de la récupération des Pokémon:', error.value);
  }
  </script>
  
  <style scoped>
  </style>
  