<script setup lang="ts">
import Main from "./components/Main.vue";
</script>

<template>
  <Main />
</template>

<style>
@import "./assets/base.css";


a {
  text-decoration: none;
  color: hsla(160, 100%, 37%, 1);
  transition: 0.4s;
}

@media (hover: hover) {
  a:hover {
    background-color: hsla(160, 100%, 37%, 0.2);
  }
}


</style>
