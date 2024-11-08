<template>
    <div v-if="pokemonList.length > 0">
      <ul>
        <li v-for="pokemon in pokemonList" :key="pokemon.name">
          {{ pokemon.name }}
        </li>
      </ul>
    </div>
  </template>
  
  <script setup lang="ts">
  import { defineProps } from 'vue';
  
  const props = defineProps({
    pokemonList: {
      type: Array,
      required: true,
      default: () => []
    }
  });
  </script>
  
  <style scoped>
  ul {
    list-style-type: none;
    padding: 0;
  }
  
  li {
    padding: 5px 0;
  }
  </style>
  