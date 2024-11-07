<template>

</template>

<script setup>

async function addApi() {
  const Api = await $fetch('https://pokeapi.co/api/v2/pokemon/', {
    method: 'POST',
  })
}

</script>

<style>

</style>