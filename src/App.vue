<template>
  <div class="layoutContainer">
    <Layout  msg="Currency Converter"/>
    <Footer/>
  </div>
  

</template>

<script>
import Layout from './components/Layout.vue'
import Footer from './components/Footer.vue'

export default {
  name: 'App',
  components: {
    Layout,
    Footer,
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white;
  position: relative;
  display: flex;
  justify-content: center;
  background-color: rgb(181, 215, 253);
}
html, body {
    margin: 0;
    height: 100%;
    background-image: url("./assets/layoutback.jpg");
    background-repeat: no-repeat;
    background-position: center;
}
.layoutContainer {
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
}
</style>
