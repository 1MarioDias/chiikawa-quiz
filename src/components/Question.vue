<script setup>
import { ref, computed, onMounted } from "vue";
import quiz from "../assets/questions.json";

const currentQuestionIndex = ref(0);
const score = ref(0);
const showResults = ref(false);
const userAnswers = ref([]);
const selectedAnswer = ref(null);
const isAnswered = ref(false);

const currentQuestion = computed(() => quiz.quiz[currentQuestionIndex.value]);

const characters = [
  'chiikawa.jpeg',
  'hachiware.jpeg',
  'usagi.jpeg',
  'kani.jpeg',
  'kurimanju.jpeg',
  'momonga.jpeg',
  'rakko.jpeg',
  'shisa.jpeg'
];

const questionCharacters = ref([]);

onMounted(() => {
  const shuffledCharacters = [...characters].sort(() => Math.random() - 0.5);
  questionCharacters.value = quiz.quiz.map((_, index) => 
    shuffledCharacters[index % characters.length]
  );
});

const currentCharacter = computed(() => 
  questionCharacters.value[currentQuestionIndex.value] || characters[0]
);

const handleAnswer = (answer) => {
  if (isAnswered.value) return;
  
  selectedAnswer.value = answer;
  isAnswered.value = true;

  userAnswers.value[currentQuestionIndex.value] = {
    question: currentQuestion.value.pergunta,
    selected: answer,
    correct: answer === currentQuestion.value.resposta_correta
  };

  if (answer === currentQuestion.value.resposta_correta) {
    score.value++;
  }
};

const nextQuestion = () => {
  if (currentQuestionIndex.value < quiz.quiz.length - 1) {
    currentQuestionIndex.value++;
    resetQuestionState();
  } else {
    showResults.value = true;
  }
};

const previousQuestion = () => {
  if (currentQuestionIndex.value > 0) {
    currentQuestionIndex.value--;
    resetQuestionState();
  }
};

const resetQuestionState = () => {
  selectedAnswer.value = userAnswers.value[currentQuestionIndex.value]?.selected || null;
  isAnswered.value = !!userAnswers.value[currentQuestionIndex.value];
};

const restartQuiz = () => {
  currentQuestionIndex.value = 0;
  score.value = 0;
  showResults.value = false;
  userAnswers.value = [];
  selectedAnswer.value = null;
  isAnswered.value = false;
};

const getButtonClasses = (option) => {
  if (!isAnswered.value || selectedAnswer.value !== option) {
    return 'w-full p-3 text-left border rounded-lg hover:bg-green-100 transition-colors duration-200';
  }
  
  const isCorrect = option === currentQuestion.value.resposta_correta;
  return `w-full p-3 text-left border rounded-lg transition-colors duration-200 ${
    isCorrect ? 'bg-green-200 border-green-500' : 'bg-red-200 border-red-500'
  }`;
};
</script>

<template>
  <div class="min-h-[400px]">
    <!-- Quiz Questions -->
    <div v-if="!showResults" class="space-y-6">
        <div class="flex items-center justify-between mb-8">
            <p class="text-2xl text-gray-600">
            Pergunta {{ currentQuestionIndex + 1 }} de {{ quiz.quiz.length }}
            </p>
            <img 
            :src="`/${currentCharacter}`" 
            :alt="currentCharacter"
            class="h-16 w-16 object-contain"
            />
        </div>

      <div class="mb-8">
        <h2 class="text-xl font-bold mb-4">{{ currentQuestion.pergunta }}</h2>
        <div class="grid gap-3">
          <button
            v-for="option in currentQuestion.opcoes"
            :key="option"
            @click="handleAnswer(option)"
            :disabled="isAnswered"
            :class="getButtonClasses(option)"
          >
            {{ option }}
          </button>
        </div>
      </div>

      <!-- Navigation Buttons -->
      <div class="flex justify-between mt-6">
        <button
          @click="previousQuestion"
          :disabled="currentQuestionIndex === 0"
          class="px-6 py-2 bg-gray-500 text-white rounded-lg hover:bg-gray-600 transition-colors duration-200 disabled:opacity-50 disabled:cursor-not-allowed"
        >
          Pergunta Anterior
        </button>
        <button
          v-if="isAnswered"
          @click="nextQuestion"
          class="px-6 py-2 bg-green-500 text-white rounded-lg hover:bg-green-600 transition-colors duration-200"
        >
          {{ currentQuestionIndex === quiz.quiz.length - 1 ? 'Mostrar Resultados' : 'Próxima Pergunta' }}
        </button>
      </div>
    </div>

    <!-- Results Screen -->
    <div v-else class="text-center space-y-6">
      <h1 class="text-2xl font-bold mb-4">Chegaste ao fim!</h1>
      <div class="text-4xl font-bold text-green-600 mb-6">
        Score: {{ score }}/{{ quiz.quiz.length }}
        <p class="text-xl text-gray-600 p-2">
          ({{ Math.round((score / quiz.quiz.length) * 100) }}%)
        </p>
      </div>

      <!-- Answer Review -->
      <div class="max-w-md mx-auto text-left space-y-4">
        <div
          v-for="(answer, index) in userAnswers"
          :key="index"
          class="p-4 rounded-lg"
          :class="answer.correct ? 'bg-green-300' : 'bg-red-300'"
        >
          <h2 class="text-xl font-semibold">{{ answer.question }}</h2>
          <p class="text-xl">A tua resposta: {{ answer.selected }}</p>
        </div>
      </div>

      <button
        @click="restartQuiz"
        class="mt-6 px-6 py-2 bg-green-500 text-white rounded-lg hover:bg-green-600 transition-colors duration-200"
      >
        Tentar Outra Vez
      </button>
    </div>
  </div>
</template>