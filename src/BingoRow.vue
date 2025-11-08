<template>
  <tr>
    <BingoCell v-for="(cell, index) in row" :key="index" :value="cell" />
  </tr>
</template>

<script>
import BingoCell from './BingoCell.vue'

export default {
  name: 'BingoRow',
  components: { BingoCell },
  props: {
    row: {
      type: Array,
      required: true,
    },
  },
}
</script>
