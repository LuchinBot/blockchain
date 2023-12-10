<script setup>
import { RouterLink, RouterView } from "vue-router";
</script>

<template>
  <b-navbar toggleable="lg" type="dark" class="navbar-base">
    <b-container>

      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

      <b-collapse id="nav-collapse" is-nav>
      <img src="./assets/blokchain.png" style="width: 130px;" alt="Descripción de la imagen">
      <b-navbar-nav class="navbar">
          <b-nav-item class="a-link" href="#">
            <router-link to="/">Vizualizar registros</router-link>
          </b-nav-item>
          <b-nav-item class="a-link" href="#">
            <router-link to="/crear_registro" class="active">Nuevo registro</router-link>
          </b-nav-item>
        </b-navbar-nav>

      </b-collapse>
    </b-container>
  </b-navbar>
  <RouterView />

</template>

<style scoped>
  .navbar-base{
    background-color: #0E0E0E;
    border-top: 10px solid #000
  }
  .navbar{
    width: 100%; display: flex; justify-content: flex-end; font-size: 12px;
  }
  a {
    border-radius: 20px;
    transition: 0.4s;
    padding: 0.5rem 1rem;
    text-decoration: none;
    margin-right: 0.3rem;
    color: #d2d2d2;
  }

  a:hover {
    color: #fff;
    background-color: #252525;
  }
  .active{
    background-color: #252525;
    font-weight: bold;
  }
  .active:hover{
    color: #fff;
    background-color: #E27E00;
  }
  *{
  user-select: none !important;
  }
</style>
