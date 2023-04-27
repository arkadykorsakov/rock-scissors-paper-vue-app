<script setup>
import { computed, onMounted, ref } from 'vue'
const wins = ref(0)
const draws = ref(0)
const losses = ref(0)

const choice = ref(null)
const computerChoice = ref(null)
const verdict = ref(null)

const outcomes = {
  rock: {
    rock: 'draw',
    paper: 'loss',
    scissors: 'win'
  },
  paper: {
    paper: 'draw',
    scissors: 'loss',
    rock: 'win'
  },
  scissors: {
    scissors: 'draw',
    rock: 'loss',
    paper: 'win'
  }
}
const winPercentage = computed(() => {
  const total = wins.value + draws.value + losses.value
  return total ? (wins.value / total) * 100 : 0
})
const saveGame = () => {
  localStorage.setItem('wins', wins.value)
  localStorage.setItem('draws', draws.value)
  localStorage.setItem('losses', losses.value)
}
const loadGame = () => {
  wins.value = parseInt(localStorage.getItem('wins')) || 0
  draws.value = parseInt(localStorage.getItem('draws')) || 0
  losses.value = parseInt(localStorage.getItem('losses')) || 0
}
const resetRound = () => {
  choice.value = null
  computerChoice.value = null
  verdict.value = null
}
const play = (c) => {
  choice.value = c
  const choices = ['rock', 'paper', 'scissors']
  const random = Math.floor(Math.random() * choices.length)
  computerChoice.value = choices[random]

  const outcome = outcomes[c][computerChoice.value]

  if (outcome === 'win') {
    wins.value++
    verdict.value = 'Ты победил :)'
  } else if (outcome === 'loss') {
    losses.value++
    verdict.value = 'Ты проиграл :('
  } else {
    draws.value++
    verdict.value = 'Ничья :|'
  }
  saveGame()
}
const translate = (value) => {
  const translation = { rock: 'Камень', scissors: 'Ножницы', paper: 'Бумага' }
  return translation[value] ? translation[value] : 'Ошибка перевода'
}
onMounted(() => {
  loadGame()
  window.addEventListener('keypress', (e) => {
    if (e.key === 'r') {
      resetRound()
    }
  })
})
</script>

<template>
  <div class="bg-gray-700 text-white text-center min-h-screen flex flex-col">
    <header class="container mx-auto p-6">
      <h1 class="text-4xl font-bold">Камень, ножницы, бумага</h1>
      <div class="container mx-auto p-6 flex-1">
        <div
          class="flex-items-center justify-center mx-6"
          v-if="choice === null"
        >
          <button
            class="bg-white rounded-full shadow-lg w-64 p-12 mx-6 transition-colors duration hover:bg-pink-500"
            @click="play('rock')"
          >
            <img src="./assets/RockIcon.svg" alt="Rock" class="w-full" />
          </button>
          <button
            class="bg-white rounded-full shadow-lg w-64 p-12 mx-6 transition-colors duration hover:bg-yellow-500"
            @click="play('scissors')"
          >
            <img src="./assets/ScissorsIcon.svg" alt="Rock" class="w-full" />
          </button>
          <button
            class="bg-white rounded-full shadow-lg w-64 p-12 mx-6 transition-colors duration hover:bg-green-500"
            @click="play('paper')"
          >
            <img src="./assets/PaperIcon.svg" alt="Rock" class="w-full" />
          </button>
        </div>
        <div v-else>
          <div class="text-3xl mb-4">
            Ты выбрал <span class="text-pink-500">{{ translate(choice) }}</span>
          </div>
          <div class="text-3xl mb-4">
            Компьютер
            <span class="text-green-500">{{ translate(computerChoice) }}</span>
          </div>
          <div class="text-6xl mb-12">
            {{ verdict }}
          </div>
          <button @click="resetRound" class="bg-pink-500 text-lg py-2 px-4">
            Reset
          </button>
        </div>
        <div class="mt-12 text-3xl mb-4">
          {{ wins }}:{{ draws }}:{{ losses }}
        </div>
        <div class="text-lg">
          Процент побед: {{ Math.round(winPercentage) }}%
        </div>
      </div>
    </header>
  </div>
</template>

<style scoped></style>
