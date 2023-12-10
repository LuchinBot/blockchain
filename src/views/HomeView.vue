<script setup>
import { ref, onMounted } from "vue";
import { conection } from "../conection/conn";

const registros = ref([]);
const loading = ref(true);

function descargarArchivo(nombreArchivo) {
  const archivos = JSON.parse(localStorage.getItem("archivos") || "{}");
  const archivoDataUrl = archivos[nombreArchivo];
  if (!archivoDataUrl) {
    alert("Archivo no encontrado.");
    return;
  }

  // Crea un enlace y descarga el archivo
  const link = document.createElement("a");
  link.href = archivoDataUrl;
  link.setAttribute("download", nombreArchivo);
  document.body.appendChild(link);
  link.click();
  document.body.removeChild(link); // No olvides limpiar el DOM
}

async function listarRegistros() {
  try {
    const total = await conection.contrato.methods.totalRegistros().call();
    let tempRegistros = [];

    for (let i = 0; i < total; i++) {
      let registro = await conection.contrato.methods.obtenerRegistro(i).call();
      tempRegistros.push(registro);
    }

    registros.value = tempRegistros;
    loading.value = false;
  } catch (error) {
    console.error("Error al listar registros:", error);
  }
}

onMounted(() => {
  listarRegistros();
});
</script>

<template>
  <div style="background-color: #1B1B1B; padding: 10px">
      <div class="container py-4">
        <h1 style="color: white; font-weight: 600; font-size: 18px">Listado de Registros</h1>
        <hr style="color: #d2d2d2">
      </div>
  </div>
  <main class="container mt-3">
      <table class="table table-hover table-bordered">
        <thead>
          <tr class="table-head">
            <th>Nro.</th>
            <th>Nombre del contrato</th>
            <th>Categoría del contrato</th>
            <th>Archivo de contrato</th>
          </tr>
        </thead>
        <tbody v-if="registros.length > 0">
          <tr v-for="(registro, index) in registros" :key="index">
            <td>{{ registro.id }}</td>
            <td>{{ registro.nombre }}</td>
            <td>{{ registro.categoria }}</td>
            <td>
              <button class="btn btn-primary" @click="descargarArchivo(registro.urlArchivo)">
                Descargar archivo
              </button>
            </td>
          </tr>
        </tbody>
        <tbody v-else-if="loading">
          <tr>
            <td class="text-center" colspan="4">Cargando registros...</td>
          </tr>
        </tbody>
        <tbody v-else>
          <tr>
            <td class="text-center" colspan="4">No hay registros.</td>
          </tr>
        </tbody>
      </table>
  </main >
</template >
  <style scoped>
    table{
         font-size: 12px;
    }
    table tbody tr td{
      text-align: center;
      background-color: #d2d2d2;
    }
    table thead{
      border: none !important;
    }
    .table-head th{
      text-align: center;
      border: none !important;
      background-color: #000;
      font-weight: 600;
      color: white;
    }
    *{
      user-select: none !important;
    }
  </style>