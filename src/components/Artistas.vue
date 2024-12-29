<template>
    <div class="p-4">
      <h1 class="text-2xl font-bold mb-4">Artistas</h1>
      <form @submit.prevent="agregarArtista" class="mb-4">
        <input v-model="nuevoArtista.nombre" placeholder="Nombre" class="border p-2 mr-2" />
        <input v-model="nuevoArtista.imagen" placeholder="URL de la imagen" class="border p-2 mr-2" />
        <input v-model="nuevoArtista.genero" placeholder="Género" class="border p-2 mr-2" />
        <button class="bg-blue-500 text-white p-2">Agregar</button>
      </form>
      <div class="grid grid-cols-3 gap-4">
        <div v-for="artista in artistas" :key="artista.id" class="border p-4">
          <img :src="artista.imagen" alt="" class="w-full h-32 object-cover mb-2" />
          <h2 class="text-xl font-bold">{{ artista.nombre }}</h2>
          <p>{{ artista.genero }}</p>
          <button @click="eliminarArtista(artista.id)" class="text-red-500 mt-2">Eliminar</button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import api from '../services/api';
  
  export default {
    data() {
      return {
        artistas: [],
        nuevoArtista: {
          nombre: '',
          imagen: '',
          genero: ''
        }
      };
    },
    methods: {
      async obtenerArtistas() {
        const response = await api.get('/artistas');
        this.artistas = response.data;
      },
      async agregarArtista() {
        await api.post('/artistas', this.nuevoArtista);
        this.obtenerArtistas();
        this.nuevoArtista = { nombre: '', imagen: '', genero: '' };
      },
      async eliminarArtista(id) {
        await api.delete(`/artistas/${id}`);
        this.obtenerArtistas();
      }
    },
    mounted() {
      this.obtenerArtistas();
    }
  };
  </script>