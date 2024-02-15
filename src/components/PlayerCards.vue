<template>
  <div class="player-cards">
    <button @click="distributeCards" :disabled="!canDistributeCards">Distribute Cards</button>
    <h2>Player Cards:</h2>
    <div v-for="(player, index) in playerCards" :key="index" :class="{ player: true, winner: winner === index + 1 }">
      <h3>Player {{ index + 1 }}</h3>
      <ul>
        <li v-for="(card, idx) in player" :key="idx">{{ card }}</li>
      </ul>
    </div>
  </div>
</template>

<script lang="ts">
export default {
  props: {
    playerCards: Array,
    canDistributeCards: Boolean,
    distributeCards: Function,
    winner: Number
  }
};
</script>
