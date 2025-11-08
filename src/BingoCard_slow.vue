<template>
  <div class="bingo-card">
    <table>
      <tbody>
        <BingoRow v-for="(row, index) in card" :key="index" :row="row" />
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import BingoRow from './BingoRow.vue';

function generateCard() {
  // Paso 1: Generar 3 números únicos por cada una de las 9 columnas
  const columns = Array.from({ length: 9 }, (_, i) => {
    const min = i === 0 ? 1 : i * 10; // Rango mínimo por columna
    const max = i === 8 ? 90 : min + 9; // Rango máximo por columna
    const nums = [];

    // Generar 3 números únicos dentro del rango
    while (nums.length < 3) {
      const num = Math.floor(Math.random() * (max - min + 1)) + min;
      if (!nums.includes(num)) nums.push(num);
    }

    // Alternar orden: columnas pares descendente, impares ascendente
    return i % 2 === 0 ? nums.sort((a, b) => a - b) : nums.sort((a, b) => b - a);
  });
  console.log('columns', JSON.parse(JSON.stringify(columns)));

  // Paso 2: Inicializar matriz vacía 3x9
  const card = Array.from({ length: 3 }, () => Array(9).fill(null));
  console.log('card', JSON.parse(JSON.stringify(card)));

  // Paso 3: Asignar 2 números a 6 columnas, y 1 número a 3 columnas
  const colIndices = Array.from({ length: 9 }, (_, i) => i);
  const colsWithTwo = colIndices.sort(() => 0.5 - Math.random()).slice(0, 6);
  console.log('colsWithTwo', JSON.parse(JSON.stringify(colsWithTwo)));

  const colsWithOne = colIndices.filter((i) => !colsWithTwo.includes(i));
  console.log('colsWithOne', JSON.parse(JSON.stringify(colsWithOne)));

  // Paso 4: Distribuir números por columna
  for (const col of colsWithTwo) {
    for (let i = 0; i < 2; i++) {
      let row;
      do {
        row = Math.floor(Math.random() * 3);
      } while (card[row][col] !== null);
      console.log(
        'row: ' +
          JSON.parse(JSON.stringify(row)) +
          ' column: ' +
          JSON.parse(JSON.stringify(col)) +
          ' value: ',
        columns[col],
      );
      console.log(' ');
      card[row][col] = columns[col][row];
    }
  }

  for (const col of colsWithOne) {
    let row;
    do {
      row = Math.floor(Math.random() * 3);
    } while (card[row][col] !== null);
    card[row][col] = columns[col].pop();
  }

  // Paso 5: Validar que cada fila tenga 5 números
  for (let row = 0; row < 3; row++) {
    const filled = card[row].filter((cell) => cell !== null).length;
    if (filled !== 5) {
      // Si no cumple, regenerar todo
      return generateCard();
    }
  }

  // El cartón ya cumple todas las reglas: 5 números por fila, 1–2 por columna
  console.log('card', JSON.parse(JSON.stringify(card)));
  console.log(' ');
  return card;
}

const card = ref(generateCard());
</script>

<style lang="scss" scoped>
$bingo-border: #333;
$bingo-bg: #fff;
$empty-bg: #000;
$hover-bg: #f0c040;

table {
  font-family: Arial, Helvetica, sans-serif;
  border-collapse: collapse;
  margin: 30px auto;
  font-size: 1.5rem;

  td {
    border: 2px solid $bingo-border;
    width: 60px;
    height: 60px;
    text-align: center;
    font-weight: bold;
    background-color: $bingo-bg;
    color: $bingo-border;
    transition: all 0.2s ease-in-out;

    &:hover {
      background-color: $hover-bg;
      transform: scale(1.1);
      cursor: pointer;
    }

    &.empty {
      background-color: #000;
      color: #000;

      &:hover {
        background-color: #000;
        transform: none;
        cursor: auto;
      }
    }
  }
}
</style>
