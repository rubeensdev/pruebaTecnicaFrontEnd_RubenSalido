<template>
  <CatalogoDeProductos />
</template>

<script>
import CatalogoDeProductos from "./components/CatalogoDeProductos.vue";

export default {
  name: "App",
  components: {
    CatalogoDeProductos,
  },
};
</script>

<style>
html {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}
</style>
