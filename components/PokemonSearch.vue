<template>
    <div>
      <h2>Rechercher un Pokémon</h2>
      <input
        v-model="searchBar"
        type="text"
        placeholder="Nom du Pokémon"
        @input="updateSuggestions"
      />
      <ul v-if="suggestions.length > 0 && searchBar">
        <li 
          v-for="suggestion in suggestions"
          :key="suggestion.name"
          @click="selectPokemon(suggestion.name)"
        >
          {{ suggestion.name[0].toUpperCase() + suggestion.name.slice(1) }}
        </li>
      </ul>
      <div v-else-if="isLoading">
        <p>Chargement...</p>
      </div>
      <div v-else-if="searchBar && !filteredList.length">
        <p>Pokémon introuvable...</p>
      </div>
  
      <div v-if="pokemon">
        <h2>{{ pokemon.name[0].toUpperCase() + pokemon.name.slice(1) }}.</h2>
        <img :src="pokemon.sprites.front_default" alt="Illustration du Pokémon"/>
        <div>Type : {{ pokemon.types.map(type => type.type.name[0].toUpperCase() + type.type.name.slice(1)).join(', ') }}</div>
        <div>Taille : {{ pokemon.height }}.</div>
        <div>Poids : {{ pokemon.weight }}.</div>
        <div>Description : {{ description }}</div>
      </div>
    </div>
  </template>
  
  <script setup lang="ts">
  import { ref, computed, watch } from 'vue';
  
  const props = defineProps({
    pokemonList: {
      type: Array,
      required: true,
      default: () => []
    }
  });
  
  const searchBar = ref('');
  const suggestions = ref([]);
  const isLoading = ref(false);
  const pokemon = ref(null);
  const description = ref('');
  
  const filteredList = computed(() => {
    if (!searchBar.value) return props.pokemonList;
    return props.pokemonList.filter(pokemon =>
      pokemon.name.toUpperCase().includes(searchBar.value.toUpperCase())
    );
  });
  
  const updateSuggestions = () => {
    const query = searchBar.value.toUpperCase();
    if (!query) {
      suggestions.value = [];
      return;
    }
    isLoading.value = true;
    suggestions.value = props.pokemonList.filter(pokemon =>
      pokemon.name.toUpperCase().includes(query)
    );
    isLoading.value = false;
  };
  
  const selectPokemon = async (name: string) => {
    searchBar.value = name;
    suggestions.value = [];
    await fetchPokemonDetails(name);
  };
  
  const fetchPokemonDetails = async (name: string) => {
    try {
      const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${name}`);
      const data = await response.json();
      pokemon.value = data;
  
      const speciesResponse = await fetch(data.species.url);
      const speciesData = await speciesResponse.json();
      const flavorTextEntry = speciesData.flavor_text_entries.find(
        (entry: any) => entry.language.name === 'fr'
      );
      description.value = flavorTextEntry
        ? flavorTextEntry.flavor_text
        : 'Pas de description disponible.';
    } catch (error) {
      console.error('Erreur lors de la récupération des détails du Pokémon:', error);
      pokemon.value = null;
      description.value = '';
    }
  };
  
  watch(searchBar, updateSuggestions);
  </script>
  
  <style scoped>
  input {
    padding: 8px;
    margin-bottom: 16px;
    width: 100%;
    max-width: 400px;
  }
  
  ul {
    list-style-type: none;
    padding: 0;
  }
  
  li {
    cursor: pointer;
    padding: 5px 0;
  }
  
  img {
    max-width: 150px;
    margin-bottom: 10px;
  }
  </style>
  