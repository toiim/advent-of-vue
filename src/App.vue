<script setup lang="ts">
import TicTacToe from './components/TicTacToe/TicTacToe.vue';
</script>
<template>
  <aside>
    <h1>Advent of Vue</h1>
    <a href="/tic-tac-toe">
      Tic Tac Toe
    </a>
  </aside>

  <main>
    <TicTacToe />
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
