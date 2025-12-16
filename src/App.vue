<template>
  <div id="app">
    <Converter />
    <Answers   />
  </div>
</template>

<script>
import Converter from './components/Converter.vue';
import Answers from './components/Answers.vue';

export default {
  components: {
    Converter,
    Answers,
  },
};
</script>

<style>
/* Estilos globales */
</style>
