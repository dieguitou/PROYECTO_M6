<template>
  <div>
    <h1 class="text-4xl font-bold text-center my-4">Gestión de Música</h1>
    <Artistas />
    <Albumes />
  </div>
</template>

<script>
import Artistas from './components/Artistas.vue';
import Albumes from './components/Albumes.vue';

export default {
  components: {
    Artistas,
    Albumes,
  },
};
</script>