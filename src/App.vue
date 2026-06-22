<script setup>
import { ref, nextTick } from 'vue';
import StudentInput from './components/StudentInput.vue';
import TopicInput from './components/TopicInput.vue';
import ControlPanel from './components/ControlPanel.vue';
import GroupResults from './components/GroupResults.vue';
import AdSensePlaceholder from './components/AdSensePlaceholder.vue';

const studentsList = ref([]);
const topicsList = ref([]);
const generatedGroups = ref([]);
const leftoverStudents = ref([]);
const hasGenerated = ref(false);

const updateStudents = (list) => {
  studentsList.value = list;
};

const updateTopics = (list) => {
  topicsList.value = list;
};

// Algoritmo de barajado Fisher-Yates
const shuffle = (array) => {
  const arr = [...array];
  let currentIndex = arr.length;
  let randomIndex;

  while (currentIndex !== 0) {
    randomIndex = Math.floor(Math.random() * currentIndex);
    currentIndex--;
    [arr[currentIndex], arr[randomIndex]] = [arr[randomIndex], arr[currentIndex]];
  }
  return arr;
};

const generateGroups = ({ mode, quantity }) => {
  if (studentsList.value.length === 0) return;

  const shuffledStudents = shuffle(studentsList.value);
  let baseGroupCount = 0;
  let baseGroupSize = 0;
  let baseStudents = [];
  let leftovers = [];

  if (mode === 'groups') {
    // Cantidad total de grupos
    baseGroupCount = Math.min(quantity, shuffledStudents.length);
    if (baseGroupCount === 0) return;
    
    baseGroupSize = Math.floor(shuffledStudents.length / baseGroupCount);
    baseStudents = shuffledStudents.slice(0, baseGroupCount * baseGroupSize);
    leftovers = shuffledStudents.slice(baseGroupCount * baseGroupSize);
  } else {
    // Cantidad de estudiantes por grupo
    const groupSizeLimit = quantity;
    baseGroupCount = Math.floor(shuffledStudents.length / groupSizeLimit);
    
    if (baseGroupCount === 0) {
      baseGroupCount = 1;
      baseGroupSize = shuffledStudents.length;
      baseStudents = [...shuffledStudents];
      leftovers = [];
    } else {
      baseGroupSize = groupSizeLimit;
      baseStudents = shuffledStudents.slice(0, baseGroupCount * baseGroupSize);
      leftovers = shuffledStudents.slice(baseGroupCount * baseGroupSize);
    }
  }

  // Inicializar grupos vacíos
  const groups = Array.from({ length: baseGroupCount }, (_, i) => ({
    id: i + 1,
    name: `Grupo ${i + 1}`,
    students: [],
    topic: null
  }));

  // Distribuir estudiantes base de forma balanceada
  for (let i = 0; i < baseStudents.length; i++) {
    const groupIndex = i % baseGroupCount;
    groups[groupIndex].students.push(baseStudents[i]);
  }

  // Distribuir sobrantes aleatoriamente asegurando que no se repitan grupos hasta dar la vuelta
  if (leftovers.length > 0) {
    let groupOrder = [];
    while (groupOrder.length < leftovers.length) {
      const round = shuffle(Array.from({ length: baseGroupCount }, (_, i) => i));
      groupOrder = groupOrder.concat(round);
    }
    
    leftovers.forEach((student, index) => {
      const groupIdx = groupOrder[index];
      groups[groupIdx].students.push(student);
    });
  }

  // Asignar temas aleatoriamente
  if (topicsList.value.length > 0) {
    let shuffledTopics = shuffle(topicsList.value);
    
    groups.forEach((group, index) => {
      if (index < shuffledTopics.length) {
        group.topic = shuffledTopics[index];
      } else {
        const topicIndex = index % shuffledTopics.length;
        if (topicIndex === 0) {
          shuffledTopics = shuffle(topicsList.value);
        }
        group.topic = shuffledTopics[topicIndex];
      }
    });
  }

  generatedGroups.value = groups;
  leftoverStudents.value = leftovers;
  hasGenerated.value = true;

  // Desplazamiento suave hasta los resultados
  nextTick(() => {
    const resultsEl = document.getElementById('resultados-sorteo');
    if (resultsEl) {
      resultsEl.scrollIntoView({ behavior: 'smooth' });
    }
  });
};
</script>

<template>
  <div class="max-w-7xl mx-auto px-4 py-8">
    <!-- Hero Block -->
    <header class="bg-[#FAF9F5] border border-[#9BB080]/30 rounded-3xl p-8 md:p-12 text-center mb-8 shadow-sm flex flex-col items-center justify-center gap-4">
      <h1 class="text-6xl md:text-7xl font-sacramento text-[#385E3C] drop-shadow-sm select-none flex items-center justify-center gap-3">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10 text-[#609966] shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 14l9-5-9-5-9 5 9 5z" />
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 14l6.16-3.422a12.083 12.083 0 01.665 6.479A11.952 11.952 0 0012 20.055a11.952 11.952 0 00-6.824-2.998 12.078 12.078 0 01.665-6.479L12 14z" />
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 14l9-5-9-5-9 5 9 5zm0 0v6" />
        </svg>
        Sorteador Grupal
      </h1>
      <p class="text-base md:text-lg font-alice text-[#2B3E42]/85 max-w-xl">
        Crea grupos aleatorios y asigna temas de forma rápida y equitativa para tus clases.
      </p>
    </header>

    <!-- Top Banner AdSense (Moved below Hero) -->
    <div class="max-w-4xl mx-auto mb-8">
      <AdSensePlaceholder type="top" />
    </div>

    <!-- Main Content Layout -->
    <div class="flex flex-col lg:flex-row gap-8 mt-8">
      
      <!-- Primary Action Area -->
      <main class="flex-grow flex flex-col gap-8 lg:max-w-[calc(100%-332px)]">
        
        <!-- Textareas Inputs Grid -->
        <section class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <StudentInput @update:students="updateStudents" />
          <TopicInput @update:topics="updateTopics" />
        </section>

        <!-- Controls Section -->
        <section class="max-w-xl mx-auto w-full">
          <ControlPanel 
            :student-count="studentsList.length" 
            @generate="generateGroups"
          />
        </section>

        <!-- Results Section with animation -->
        <section v-if="hasGenerated" class="mt-4">
          <GroupResults 
            :groups="generatedGroups" 
            :leftovers="leftoverStudents"
          />
        </section>

      </main>

      <!-- Sidebar Area (AdSense) -->
      <aside class="w-full lg:w-[300px] shrink-0">
        <AdSensePlaceholder type="sidebar" />
      </aside>

    </div>

    <!-- Footer -->
    <footer class="mt-16 pt-8 border-t border-[#9BB080]/30 text-center text-xs text-[#2B3E42]/60 font-alice flex flex-col sm:flex-row items-center justify-between gap-4">
      <p>&copy; 2026 Sorteador Grupal para Docentes. Ejecutado 100% en tu navegador (respetando la privacidad de tus estudiantes).</p>
      <p>Desarrollado por <a href="https://github.com/jbam303" target="_blank" rel="noopener noreferrer" class="text-[#609966] hover:text-[#385E3C] font-semibold transition-colors">jbam303</a></p>
    </footer>
  </div>
</template>
