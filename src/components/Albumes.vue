<template>
    <div class="p-4">
      <h1 class="text-2xl font-bold mb-4">Álbumes</h1>
  
      <!-- Buscador y Filtro -->
      <div class="mb-4 flex gap-4">
        <input
          v-model="busqueda"
          placeholder="Buscar por título del álbum"
          class="border p-2 w-1/2"
        />
        <input
          v-model.number="filtroAnio"
          type="number"
          placeholder="Filtrar por año (ej: 2000)"
          class="border p-2 w-1/4"
        />
        <button @click="limpiarFiltros" class="bg-gray-500 text-white p-2">
          Limpiar Filtros
        </button>
      </div>
  
      <!-- Formulario para agregar/editar un álbum -->
      <form @submit.prevent="guardarAlbum" class="mb-4">
        <input v-model="nuevoAlbum.titulo" placeholder="Título del Álbum" class="border p-2 mr-2" />
        <input v-model="nuevoAlbum.imagen" placeholder="URL de la Imagen" class="border p-2 mr-2" />
        <input v-model.number="nuevoAlbum.anio" type="number" placeholder="Año" class="border p-2 mr-2" />
        <select v-model.number="nuevoAlbum.artista_id" class="border p-2 mr-2">
          <option disabled value="">Selecciona un artista</option>
          <option v-for="artista in artistas" :key="artista.id" :value="artista.id">
            {{ artista.nombre }}
          </option>
        </select>
        <button class="bg-blue-500 text-white p-2">{{ editando ? "Actualizar" : "Agregar" }}</button>
      </form>
  
      <!-- Tabla de álbumes -->
      <table class="table-auto w-full border-collapse border border-gray-300">
        <thead>
          <tr>
            <th class="border border-gray-300 p-2">Título</th>
            <th class="border border-gray-300 p-2">Imagen</th>
            <th class="border border-gray-300 p-2">Año</th>
            <th class="border border-gray-300 p-2">Artista</th>
            <th class="border border-gray-300 p-2">Acciones</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="album in albumesFiltrados" :key="album.id">
            <td class="border border-gray-300 p-2">{{ album.titulo }}</td>
            <td class="border border-gray-300 p-2">
              <img :src="album.imagen" alt="Imagen del Álbum" class="h-16 w-16 object-cover" />
            </td>
            <td class="border border-gray-300 p-2">{{ album.anio }}</td>
            <td class="border border-gray-300 p-2">{{ obtenerNombreArtista(album.artista_id) }}</td>
            <td class="border border-gray-300 p-2">
              <button @click="editarAlbum(album)" class="bg-yellow-500 text-white p-1 mr-2">Editar</button>
              <button @click="eliminarAlbum(album.id)" class="bg-red-500 text-white p-1">Eliminar</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>
  
  <script>
  import api from "../services/api";
  
  export default {
    data() {
      return {
        albumes: [],
        artistas: [],
        busqueda: "",
        filtroAnio: "",
        nuevoAlbum: {
          titulo: "",
          imagen: "",
          anio: "",
          artista_id: null,
        },
        editando: false,
        albumIdEditar: null,
      };
    },
    computed: {
      albumesFiltrados() {
        return this.albumes.filter((album) => {
          const coincideTitulo = album.titulo.toLowerCase().includes(this.busqueda.toLowerCase());
          const coincideAnio = this.filtroAnio ? album.anio >= this.filtroAnio : true;
          return coincideTitulo && coincideAnio;
        });
      },
    },
    methods: {
      async obtenerDatos() {
        const [albumesResponse, artistasResponse] = await Promise.all([
          api.get("/albumes"),
          api.get("/artistas"),
        ]);
        this.albumes = albumesResponse.data;
        this.artistas = artistasResponse.data;
      },
      obtenerNombreArtista(artistaId) {
        if (!artistaId) return "Desconocido";
        const artista = this.artistas.find((artista) => artista.id === artistaId);
        return artista ? artista.nombre : "Desconocido";
      },
      async guardarAlbum() {
        if (this.editando) {
          await api.put(`/albumes/${this.albumIdEditar}`, this.nuevoAlbum);
        } else {
          await api.post("/albumes", this.nuevoAlbum);
        }
        this.obtenerDatos();
        this.limpiarFormulario();
      },
      editarAlbum(album) {
        this.nuevoAlbum = { ...album };
        this.editando = true;
        this.albumIdEditar = album.id;
      },
      async eliminarAlbum(id) {
        await api.delete(`/albumes/${id}`);
        this.obtenerDatos();
      },
      limpiarFormulario() {
        this.nuevoAlbum = {
          titulo: "",
          imagen: "",
          anio: "",
          artista_id: null,
        };
        this.editando = false;
        this.albumIdEditar = null;
      },
      limpiarFiltros() {
        this.busqueda = "";
        this.filtroAnio = "";
      },
    },
    mounted() {
      this.obtenerDatos();
    },
  };
  </script>
  