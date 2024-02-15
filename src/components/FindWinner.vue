<template>
<div class="find-winner">
    <button @click="findWinner" :disabled="!canFindWinner">Find Winner</button>
    <div v-if="winner">
    <h2>Winner:</h2>
    <p>Player {{ winner }}</p>
    </div>
</div>
</template>

<script lang="ts">
import { ref } from 'vue';

export default {
props: {
    canFindWinner: Boolean,
    findWinner: Function,
    winner: Number
}
};
</script>
