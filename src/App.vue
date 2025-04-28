<template>
  <div id="app">
    <button v-if="!isShuffled" @click="shuffleCards">Começar a putaria</button>

    <!-- Exibe a carta -->
    <div v-if="isShuffled" class="card">
      <img
        :src="cards[currentIndex].image"
        :alt="cards[currentIndex].name"
        class="card-image"
      />
      <!-- <h2>{{ cards[currentIndex].name }}</h2> -->
      <p>{{ cards[currentIndex].description }}</p>

      <!-- Botão Próximo -->
      <button v-if="currentIndex < cards.length - 1" @click="nextCard">
        Próximo
      </button>

      <!-- Mensagem de reiniciar -->
      <div v-if="currentIndex >= cards.length - 1">
        <button @click="restartGame" style="background-color: red">
          Reiniciar
        </button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";

interface Card {
  id: number;
  name: string;
  image: string;
  description: string;
}

const cards = ref<Card[]>([]);
const currentIndex = ref(0); // Índice da carta atual
const isShuffled = ref(false); // Para controlar quando o embaralhamento ocorre

const shuffleCards = () => {
  // Embaralha as cartas usando o algoritmo de Fisher-Yates
  for (let i = cards.value.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [cards.value[i], cards.value[j]] = [cards.value[j], cards.value[i]]; // Troca as cartas
  }
  currentIndex.value = 0; // Reinicia o índice para a primeira carta
  isShuffled.value = true; // Marca que o embaralhamento aconteceu
};

// Função para passar para a próxima carta
const nextCard = () => {
  if (currentIndex.value < cards.value.length - 1) {
    currentIndex.value++; // Incrementa o índice para a próxima carta
  }
};

// Função para reiniciar o jogo (voltar ao começo)
const restartGame = () => {
  currentIndex.value = 0; // Reinicia o índice para a primeira carta
  shuffleCards(); // Embaralha novamente as cartas
};

// Função para buscar as cartas do arquivo JSON
const fetchCards = async () => {
  const response = await fetch("/data/cards.json");
  if (response.ok) {
    cards.value = await response.json();
  }
};

// Chama a função para buscar as cartas quando o componente é montado
onMounted(() => {
  fetchCards();
});
</script>

<style scoped>
#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin-top: 50px;
}

.card {
  border: 1px solid #ccc;
  margin: 10px;
  padding: 15px;
  width: 250px;
  text-align: center;
  background-color: #fff;
  border-radius: 8px;
}

.card img {
  max-width: 100%;
  height: auto;
  border-radius: 8px;
}

.card h2 {
  font-size: 18px;
  margin: 10px 0;
}

.card p {
  font-size: 14px;
  color: #555;
}

button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 10px 20px;
  margin-top: 10px;
  cursor: pointer;
  border-radius: 5px;
  font-size: 14px;
}

button:disabled {
  background-color: #ddd;
  cursor: not-allowed;
}

button:hover:not(:disabled) {
  background-color: #0056b3;
}
</style>
