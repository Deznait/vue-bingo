<template>
  <div class="bingo-card">
    <table>
      <tbody>
        <tr v-for="(row, rowIndex) in card" :key="rowIndex">
          <td v-for="(cell, colIndex) in row" :key="colIndex" :class="{ empty: cell === null }">
            {{ cell ?? '' }}
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref } from 'vue'

function generateCard() {
  // Paso 1: Generar 3 números por columna
  const columns = Array.from({ length: 9 }, (_, i) => {
    const min = i === 0 ? 1 : i * 10
    const max = i === 8 ? 90 : min + 9
    const nums = []
    while (nums.length < 3) {
      const num = Math.floor(Math.random() * (max - min + 1)) + min
      if (!nums.includes(num)) nums.push(num)
    }

    // Columnas pares → descendente, impares → ascendente
    return i % 2 === 0 ? nums.sort((a, b) => b - a) : nums.sort((a, b) => a - b)
  })

  // Paso 2: Inicializar matriz vacía 3x9
  const card = Array.from({ length: 3 }, () => Array(9).fill(null))

  // Paso 3: Asignar 2 números a 6 columnas, y 1 número a 3 columnas
  const colIndices = Array.from({ length: 9 }, (_, i) => i)
  const colsWithTwo = colIndices.sort(() => 0.5 - Math.random()).slice(0, 6)
  const colsWithOne = colIndices.filter((i) => !colsWithTwo.includes(i))

  // Paso 4: Distribuir números por columna
  for (const col of colsWithTwo) {
    for (let i = 0; i < 2; i++) {
      let row
      do {
        row = Math.floor(Math.random() * 3)
      } while (card[row][col] !== null)
      card[row][col] = columns[col].pop()
    }
  }

  for (const col of colsWithOne) {
    let row
    do {
      row = Math.floor(Math.random() * 3)
    } while (card[row][col] !== null)
    card[row][col] = columns[col].pop()
  }

  // Paso 5: Validar que cada fila tenga 5 números
  for (let row = 0; row < 3; row++) {
    const filled = card[row].filter((cell) => cell !== null).length
    if (filled !== 5) {
      return generateCard() // regenerar si no cumple
    }
  }

  return card
}

const card = ref(generateCard())
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
