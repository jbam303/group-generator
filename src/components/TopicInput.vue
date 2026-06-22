<template>
  <div class="bg-white p-6 rounded-2xl shadow-sm border border-[#9BB080]/30 flex flex-col gap-3">
    <div class="flex justify-between items-center">
      <h2 class="text-xl font-semibold text-[#2B3E42] flex items-center gap-2">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-[#609966]" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253" />
        </svg>
        Lista de Temas (Opcional)
      </h2>
      <span 
        class="text-xs px-2.5 py-1 rounded-full font-medium"
        :class="topicCount > 0 ? 'bg-[#9BB080]/20 text-[#385E3C]' : 'bg-[#E9EDE4] text-[#2B3E42]/60'"
      >
        {{ topicCount }} {{ topicCount === 1 ? 'tema' : 'temas' }}
      </span>
    </div>
    
    <p class="text-xs text-[#2B3E42]/70 leading-relaxed">
      Ingresa los temas de disertación/proyecto (uno por línea). Si hay menos temas que grupos, se repetirán.
    </p>

    <textarea
      id="topic-list-textarea"
      v-model="rawInput"
      @input="saveToLocalStorage"
      placeholder="Ejemplo:&#10;Tema A: Inteligencia Artificial&#10;Tema B: Redes Neuronales&#10;Tema C: Computación Cuántica"
      class="w-full h-64 p-4 rounded-xl border border-[#9BB080]/40 focus:border-[#609966] focus:ring-2 focus:ring-[#609966]/20 outline-none transition-all duration-300 resize-none font-alice text-[#2B3E42] bg-[#F6F7F3]/30"
    ></textarea>
    
    <div class="flex justify-between items-center">
      <button 
        type="button" 
        @click="clearInput"
        class="text-xs text-[#2B3E42]/50 hover:text-red-500 font-medium transition-colors"
        :disabled="!rawInput"
        :class="{'opacity-50 cursor-not-allowed': !rawInput}"
      >
        Limpiar lista
      </button>
      <button 
        type="button" 
        @click="loadDemo"
        class="text-xs text-[#609966] hover:text-[#385E3C] font-semibold transition-colors"
      >
        Cargar temas de ejemplo
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const rawInput = ref('');

const topicCount = computed(() => {
  return rawInput.value
    .split('\n')
    .map(topic => topic.trim())
    .filter(topic => topic.length > 0)
    .length;
});

const emit = defineEmits(['update:topics']);

const saveToLocalStorage = () => {
  localStorage.setItem('topic_list', rawInput.value);
  emit('update:topics', getCleanTopicsList());
};

const getCleanTopicsList = () => {
  return rawInput.value
    .split('\n')
    .map(topic => topic.trim())
    .filter(topic => topic.length > 0);
};

const clearInput = () => {
  rawInput.value = '';
  saveToLocalStorage();
};

const loadDemo = () => {
  rawInput.value = [
    'Tema 1: Revolución Industrial y sus efectos sociales',
    'Tema 2: Descubrimiento de América e intercambio cultural',
    'Tema 3: La Antigua Roma y sus aportes al derecho',
    'Tema 4: Revolución Francesa y los Derechos Humanos',
    'Tema 5: La Ruta de la Seda y la globalización comercial',
    'Tema 6: Civilización Maya: astronomía y arquitectura'
  ].join('\n');
  saveToLocalStorage();
};

onMounted(() => {
  const saved = localStorage.getItem('topic_list');
  if (saved !== null) {
    rawInput.value = saved;
  } else {
    // Cargar demo por defecto si está vacío
    loadDemo();
  }
  emit('update:topics', getCleanTopicsList());
});
</script>
