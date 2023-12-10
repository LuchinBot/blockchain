<script setup>
import { ref } from "vue";
import { conection } from "../conection/conn";

const nombre = ref("");
const categoria = ref("");
const archivo = ref(null); // Referencia al archivo

const success = ref(false);
const errorMensaje = ref("");

// Maneja la carga del archivo
function handleFileUpload(event) {
  archivo.value = event.target.files[0];
}

// Obtén la cuenta de Ethereum
async function getAccount() {
  const accounts = await window.ethereum.request({
    method: "eth_requestAccounts",
  });
  return accounts[0];
}

// Crea el registro
async function crearRegistro() {
  const cuenta = await getAccount();
  if (!archivo.value) {
    errorMensaje.value = "Por favor, selecciona un archivo para subir.";
    return;
  }
  const nombreArchivo = archivo.value.name; // Usa el nombre del archivo directamente

  try {
    const estimatedGas = await conection.contrato.methods
      .crearRegistro(nombre.value, categoria.value, nombreArchivo)
      .estimateGas({ from: cuenta });

    const gasLimit = Math.floor(Number(estimatedGas) * 1.2);

    await conection.contrato.methods
      .crearRegistro(nombre.value, categoria.value, nombreArchivo)
      .send({ from: cuenta, gas: gasLimit.toString() });

    // Guarda el archivo en localStorage
    const reader = new FileReader();
    reader.onload = function (e) {
      const archivos = JSON.parse(localStorage.getItem("archivos") || "{}");
      archivos[nombreArchivo] = e.target.result; // Guarda el contenido del archivo
      localStorage.setItem("archivos", JSON.stringify(archivos));
    };
    reader.readAsDataURL(archivo.value); // Lee el archivo como Data URL

    nombre.value = "";
    categoria.value = "";
    archivo.value = null;
    success.value = true;
    errorMensaje.value = "";
  } catch (error) {
    console.error("Error al crear el registro:", error);
    errorMensaje.value = error.message;
    success.value = false;
  }
}
</script>

<template>
<div style="background-color: #1B1B1B; padding: 10px">
      <div class="container py-4">
        <h1 style="color: white; font-weight: 600; font-size: 18px">Creación de un Registro</h1>
        <hr style="color: #d2d2d2">
      </div>
  </div>
  <main class="container my-3">
    <form @submit.prevent="crearRegistro">
      <div class="form-group mb-3">
        <label class="mb-1 text-secondary mb-2" for="nombreinput">Nombre del contrato:</label>
        <input v-model="nombre" type="text" class="form-control rounded-0" id="nombreinput" placeholder="Ingrese el nombre del contrato"
          required />
      </div>
      <div class="form-group mb-3">
        <label class="mb-1 text-secondary mb-2" for="inputcategoria">Categoría del contrato:</label>
        <input v-model="categoria" type="text" class="form-control rounded-0" id="inputcategoria" placeholder="Ingrese la categoria del contrato"
          required />
      </div>
      <div class="form-group mb-3">
        <label class="mb-1 text-secondary mb-2" for="inputarchivo">Archivo del contrato:</label>
        <input type="file" class="form-control rounded-0" id="inputarchivo" placeholder="Ingrese el archivo del archivo del contrato"
          @change="handleFileUpload" required />
      </div>
      <button type="submit" class="btn btn-save rounded-0 px-5 mt-3">Guardar Registro</button>
    </form>
    <p class="text" v-if="success">Registro creado exitosamente.</p>
    <p class="text" v-if="errorMensaje">{{ errorMensaje }}</p>
  </main>
</template>

<style scoped>
.h2 {
  margin: 1rem 0;
}

.text {
  margin: 0.5rem 0;
}
.form-control{
  color: white !important;
    background-color: #1B1B1B !important;
    border: none !important;
    border-bottom: 1px solid #d2d2d2 !important;
}
.form-control:focus{
  border-color: none !important;
  box-shadow: none !important;
}
.form-control::placeholder{
  color: #33454C !important;
}
.btn-save{
  transition: 0.4s;
  font-weight: 600;
  background-color: #1B1B1B ; 
  border: 1px solid #FF9000;
  color: white
}
.btn-save:hover{
  color: #f1f1f1;
  background-color: #FF9000;
}
</style>
