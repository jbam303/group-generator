<template>
  <div class="bg-white p-6 rounded-2xl shadow-sm border border-[#9BB080]/30 flex flex-col gap-3">
    <div class="flex justify-between items-center">
      <h2 class="text-xl font-semibold text-[#2B3E42] flex items-center gap-2">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-[#609966]" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4.354a4 4 0 110 5.292M15 21H3v-1a6 6 0 0112 0v1zm0 0h6v-1a6 6 0 00-9-5.197M13 7a4 4 0 11-8 0 4 4 0 018 0z" />
        </svg>
        Lista de Estudiantes
      </h2>
      <span 
        class="text-xs px-2.5 py-1 rounded-full font-medium"
        :class="studentCount > 0 ? 'bg-[#9BB080]/20 text-[#385E3C]' : 'bg-[#E9EDE4] text-[#2B3E42]/60'"
      >
        {{ studentCount }} {{ studentCount === 1 ? 'estudiante' : 'estudiantes' }}
      </span>
    </div>
    
    <p class="text-xs text-[#2B3E42]/70 leading-relaxed">
      Ingresa los nombres de los estudiantes, uno por línea. Las líneas vacías serán omitidas de forma automática.
    </p>

    <textarea
      id="student-list-textarea"
      v-model="rawInput"
      @input="saveToLocalStorage"
      placeholder="Ejemplo:&#10;Juan Pérez&#10;María Gómez&#10;Carlos López"
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
        Cargar lista de ejemplo
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const rawInput = ref('');

const studentCount = computed(() => {
  return rawInput.value
    .split('\n')
    .map(name => name.trim())
    .filter(name => name.length > 0)
    .length;
});

const emit = defineEmits(['update:students']);

const saveToLocalStorage = () => {
  localStorage.setItem('student_list', rawInput.value);
  emit('update:students', getCleanStudentsList());
};

const getCleanStudentsList = () => {
  return rawInput.value
    .split('\n')
    .map(name => name.trim())
    .filter(name => name.length > 0);
};

const clearInput = () => {
  rawInput.value = '';
  saveToLocalStorage();
};

const loadDemo = () => {
  rawInput.value = [
    'Sofía Rodríguez',
    'Mateo Fernández',
    'Valentina Gómez',
    'Santiago López',
    'Camila Díaz',
    'Bautista Pérez',
    'Isabella Romero',
    'Benjamín Torres',
    'Catalina Silva',
    'Felipe Acosta',
    'Martina Ruiz',
    'Joaquín Castro',
    'Lucía Giménez',
    'Nicolás Peralta',
    'Delfina Sosa',
    'Tomás Herrera',
    'Emma Maidana',
    'Bruno Benítez'
  ].join('\n');
  saveToLocalStorage();
};

onMounted(() => {
  const saved = localStorage.getItem('student_list');
  if (saved !== null) {
    rawInput.value = saved;
  } else {
    // Cargar demo por defecto si está vacío
    loadDemo();
  }
  emit('update:students', getCleanStudentsList());
});
</script>
