<template>
  <div class='container' id="app"> 
    <div> 
      <game-component msg="Typing speed test made with vue js"/>
    </div>
  </div>
</template>

<script>
import GameComponent from './components/Game.vue'  

export default {
  name: 'App',
  components: {
   GameComponent 
  },
}
</script>

<style>
/* #app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
} */
</style>
