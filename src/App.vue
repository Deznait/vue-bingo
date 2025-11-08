<template>
  <BingoCard :numerosExtraidos="numerosExtraidos" :key="cardKey" />
    <button class="boton boton--rojo" @click="reiniciar">Reiniciar juego</button>
  <NumberFrame ref="frameRef" @nuevo-numero="agregarNumero" />
</template>

<script setup>
import { ref } from 'vue';

import BingoCard from './BingoCard.vue';
import NumberFrame from './NumberFrame.vue';

const numerosExtraidos = ref([]);
const cardKey = ref(0); // clave reactiva para forzar recreación del componente
const frameRef = ref(null); // referencia al componente NumberFrame

function agregarNumero(n) {
  if (!numerosExtraidos.value.includes(n)) {
    numerosExtraidos.value.push(n);
  }
}

function reiniciar() {
  numerosExtraidos.value = [];
  cardKey.value++; // cambia la clave → fuerza que BingoCard se vuelva a montar

  // 👇 Llama al método expuesto desde NumberFrame
  frameRef.value?.reiniciarNumeros();
}
</script>

<style lang="scss">
#app {
  font-family: Arial, Helvetica, sans-serif;
  max-width: 60rem;
  margin: 0 auto;
  font-size: 1.5rem;
}

.boton {
    padding: 10px 20px;
    font-weight: bold;
    border: none;
    border-radius: 6px;
    cursor: pointer;
    font-size: 1rem;
    transition: background-color 0.3s ease;
    margin: 0 10px;

    &.boton--verde {
      background-color: #4caf50;
      color: white;
    }

    &.boton--rojo {
      background-color: #e53935;
      color: white;
    }
  }
</style>
