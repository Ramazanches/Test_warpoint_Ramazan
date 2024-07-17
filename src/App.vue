<script setup>

import Tarif from './components/Tarif.vue'

</script>

<template>

  <Tarif />

</template>

<style>

* { 
  box-sizing: border-box 
}

html, 
body { 
  font-family: "Open Sans", "MalgunGothic", "Arial", sans-serif 
}

h1 { 
  color: #37373f 
}

h2 {
  margin: 0 0 8px;
  font-size: 16px;
}

a {
  border-bottom: 1px dotted #525252;
  text-decoration: none;
  color: #525252;
}

a:hover {
  border-bottom: 1px solid #343434;
  color: #343434;
}

p { 
  margin: 4px 0 
}

hr {
  margin: 20px 8px;
  height: 1px;
  border: none;
  background-color: #d7d7e5;
}
</style>
