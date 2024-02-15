<template>
  <div class="card-game">
    <ShuffleCards 
      :clearWinner="clearWinner"
      :playerCards="playerCards"
      :canDistributeCards="canDistributeCards"
      :shuffledCards="shuffledCards"
      @update:shuffledCards="updateShuffledCards"
      @update:playerCards="clearPlayerCards"
    />
    <PlayerCards
      :playerCards="playerCards"
      :canDistributeCards="canDistributeCards"
      :distributeCards="distributeCards"
      :winner="winner"
      
    />
    <FindWinner
      :canFindWinner="canFindWinner"
      :findWinner="findWinner"
      :winner="winner"
    />
  </div>
</template>

<script lang="ts">
import { ref } from 'vue';
import ShuffleCards from './components/ShuffleCards.vue';
import PlayerCards from './components/PlayerCards.vue';
import FindWinner from './components/FindWinner.vue';

export default {
    setup() {
        const playerCards = ref<Array<string[]>>([[], [], [], []]);
        const winner = ref<null | number>(null);
        const canFindWinner = ref(false);
        const canDistributeCards = ref(true);
        const shuffledCards = ref<string[]>([]);

        const distributeCards = () => {
            // clear last time winner result and CSS effects
            clearWinner();
            // Clear player cards
            playerCards.value = [[], [], [], []];
            // Distribute shuffled cards to players
            for (let i = 0; i < shuffledCards.value.length; i++) {
                playerCards.value[i % 4].push(shuffledCards.value[i]);
            }
            shuffledCards.value = [];
            canFindWinner.value = true;
            canDistributeCards.value = false;
        };
        const findWinner = () => {
            if (playerCards.value.some(cards => cards.length === 0)) return
            
            // re-order players' cards in hand
            const order = "234567891JQKA";
            const suitOrder = "@#^*";
            const sortedPlayerCards = playerCards.value.map(cards => {
                return cards.sort((a, b) => {
                    const cardAValue = order.indexOf(a[0]) * 10 + suitOrder.indexOf(a[1]);
                    const cardBValue = order.indexOf(b[0]) * 10 + suitOrder.indexOf(b[1]);
                    return cardAValue - cardBValue;
                });
            });
            // find the player with the most same number cards
            const maxSameNumberCards = sortedPlayerCards.map(cards => {
                const sameNumberCards = cards.reduce((acc, card) => {
                    acc[card[0]] = (acc[card[0]] || 0) + 1;
                    return acc;
                }, ({} as {
                    [key: string]: number;
                }));
                const maxCount = Math.max(...Object.values(sameNumberCards));
                return Object.keys(sameNumberCards).filter(key => sameNumberCards[key] === maxCount);
            });
            // find the player with the most same suit cards
            let maxIndex = 0;
            let maxCardValue = -1;
            maxSameNumberCards.forEach((cards, index) => {
                const cardValue = order.indexOf(cards[0]) * 10 + suitOrder.indexOf(cards[1]);
                if (cards.length === 1 && cardValue > maxCardValue) {
                    maxIndex = index;
                    maxCardValue = cardValue;
                }
            });
            // mark the winner
            winner.value = maxIndex + 1;
            // mark the winner in page:
            const playerElements = document.querySelectorAll('.player');
            playerElements.forEach((element, index) => {
                if (index === maxIndex) {
                    element.classList.add('winner');
                }
                else {
                    element.classList.remove('winner');
                }
            });
        };
        const clearWinner = () => {
            winner.value = null;
            const playerElements = document.querySelectorAll('.player');
            playerElements.forEach((element) => {
                element.classList.remove('winner');
            });
        };

        const updateShuffledCards = (newShuffledCards: string[]) => {
          shuffledCards.value = newShuffledCards;
        };

        const clearPlayerCards = () => {
          playerCards.value = [[], [], [], []];
          canDistributeCards.value = true;
          clearWinner();
        }
        return {
          playerCards,
          winner,
          distributeCards,
          canFindWinner,
          findWinner,
          canDistributeCards,
          clearWinner,
          shuffledCards,
          updateShuffledCards,
          clearPlayerCards
        };
    },
    components: { ShuffleCards, PlayerCards, FindWinner }
};
</script>

<style scoped>
.winner {
  border: 2px solid green;
}

.card-game {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
}
</style>
