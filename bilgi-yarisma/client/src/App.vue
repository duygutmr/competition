<template>
   <div class="container">
 <hello-world></hello-world>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'
import axios from 'axios'
export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<style>
html {
  font-family:Cambria, Cochin, Georgia, Times, 'Times New Roman', serif;
  background-image: url('./assets/1419.jpg');
  background-size: 100% 150%;
  background-repeat: no-repeat;
}
</style>
