<template>
  <div class="flex flex-col gap-6 w-full scroll-mt-6" id="resultados-sorteo">
    <div class="flex flex-col md:flex-row justify-between items-start md:items-center gap-4">
      <div>
        <h2 class="text-2xl font-bold text-[#2B3E42] flex items-center gap-2">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-[#609966]" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-3 7h3m-3 4h3m-6-4h.01M9 16h.01" />
          </svg>
          Grupos Generados
        </h2>
        <p class="text-xs text-[#2B3E42]/70">
          Se formaron {{ groups.length }} grupos con los estudiantes ingresados.
        </p>
      </div>
      
      <!-- Action buttons like print/copy -->
      <div class="flex gap-2">
        <button
          type="button"
          @click="copyResults"
          class="flex items-center gap-1.5 px-3 py-1.5 rounded-lg border border-[#9BB080]/40 text-[#2B3E42]/80 hover:bg-[#E9EDE4] hover:text-[#2B3E42] text-xs font-semibold transition-all duration-300"
        >
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 5H6a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2v-1M8 5a2 2 0 002 2h2a2 2 0 002-2M8 5a2 2 0 012-2h2a2 2 0 012 2m0 0h2a2 2 0 012 2v3m2 4H10m0 0l3-3m-3 3l3 3" />
          </svg>
          {{ copied ? '¡Copiado!' : 'Copiar Resultados' }}
        </button>
        <button
          type="button"
          @click="printResults"
          class="flex items-center gap-1.5 px-3 py-1.5 rounded-lg bg-[#9BB080]/20 text-[#385E3C] hover:bg-[#9BB080]/30 text-xs font-semibold transition-all duration-300"
        >
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 17h2a2 2 0 002-2v-4a2 2 0 00-2-2H5a2 2 0 00-2 2v4a2 2 0 002 2h2m2 4h6a2 2 0 002-2v-4a2 2 0 00-2-2H9a2 2 0 00-2 2v4a2 2 0 002 2zm8-12V5a2 2 0 00-2-2H9a2 2 0 00-2 2v4h10z" />
          </svg>
          Imprimir
        </button>
      </div>
    </div>

    <!-- Alert for leftover students (Reassigned) -->
    <div 
      v-if="leftovers && leftovers.length > 0"
      class="bg-[#E9EDE4] border-l-4 border-[#609966] p-4 rounded-r-xl shadow-sm pulse-soft flex flex-col sm:flex-row items-start sm:items-center justify-between gap-3"
    >
      <div class="flex items-start gap-3">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-[#609966] shrink-0 mt-0.5" viewBox="0 0 20 20" fill="currentColor">
          <path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7-4a1 1 0 11-2 0 1 1 0 012 0zM9 9a1 1 0 000 2v3a1 1 0 001 1h1a1 1 0 100-2v-3a1 1 0 00-1-1H9z" clip-rule="evenodd" />
        </svg>
        <div>
          <h4 class="font-bold text-[#2B3E42] text-sm sm:text-base">Distribución Equitativa de Sobrantes</h4>
          <p class="text-xs text-[#2B3E42]/80 mt-0.5">
            Los siguientes estudiantes no completaban un grupo extra, por lo que fueron distribuidos al azar en los grupos existentes:
          </p>
          <div class="flex flex-wrap gap-1.5 mt-2">
            <span 
              v-for="student in leftovers" 
              :key="student"
              class="px-2 py-0.5 bg-[#9BB080]/30 text-[#385E3C] text-xs font-semibold rounded-md border border-[#9BB080]/40"
            >
              {{ student }}
            </span>
          </div>
        </div>
      </div>
    </div>

    <!-- Groups Grid -->
    <transition-group 
      name="list" 
      tag="div" 
      class="grid grid-cols-1 md:grid-cols-2 gap-6"
    >
      <template v-for="(group, index) in groups" :key="group.id">
        <!-- Render AdSense Placeholder after the 3rd group (index 2) -->
        <div 
          v-if="index === 3" 
          class="col-span-1 md:col-span-2"
          key="native-ad-slot"
        >
          <AdSensePlaceholder type="native" />
        </div>

        <div 
          class="bg-white rounded-2xl shadow-sm border border-[#9BB080]/20 overflow-hidden flex flex-col hover:shadow-md hover:border-[#609966]/40 transition-all duration-300 transform hover:-translate-y-0.5"
        >
          <!-- Group Header -->
          <div class="bg-[#E9EDE4] p-4 border-b border-[#9BB080]/20 flex justify-between items-center">
            <h3 class="font-bold text-[#2B3E42] text-lg flex items-center gap-2">
              <span class="w-6 h-6 rounded-full bg-[#9BB080] text-white flex items-center justify-center text-xs font-bold">{{ index + 1 }}</span>
              {{ group.name }}
            </h3>
            <span class="text-xs text-[#2B3E42]/60 font-semibold bg-white/70 px-2 py-0.5 rounded-md border border-[#9BB080]/10">
              {{ group.students.length }} alumnos
            </span>
          </div>

          <!-- Group Content -->
          <div class="p-5 flex-grow flex flex-col gap-4">
            <!-- Topic if assigned -->
            <div 
              v-if="group.topic"
              class="bg-[#F6F7F3] p-3 rounded-xl border border-[#9BB080]/25 flex items-start gap-2.5"
            >
              <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-[#609966] shrink-0 mt-0.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z" />
              </svg>
              <div>
                <span class="text-[10px] uppercase font-bold text-[#609966] tracking-wider block">Tema asignado</span>
                <span class="text-sm font-semibold text-[#2B3E42] leading-tight block mt-0.5">{{ group.topic }}</span>
              </div>
            </div>

            <!-- Student List -->
            <div class="flex-grow">
              <span class="text-[10px] uppercase font-bold text-[#2B3E42]/60 tracking-wider block mb-2">Miembros</span>
              <ul class="divide-y divide-[#E9EDE4] border border-[#E9EDE4] rounded-xl overflow-hidden bg-[#F6F7F3]/10">
                <li 
                  v-for="(student, sIdx) in group.students" 
                  :key="student"
                  class="px-4 py-2.5 text-sm text-[#2B3E42] flex items-center justify-between hover:bg-[#F6F7F3]/40 transition-colors"
                >
                  <span class="font-medium">{{ student }}</span>
                  <span class="text-[10px] text-[#2B3E42]/40 italic">#{{ sIdx + 1 }}</span>
                </li>
              </ul>
            </div>
          </div>
        </div>
      </template>
    </transition-group>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import AdSensePlaceholder from './AdSensePlaceholder.vue';

const props = defineProps({
  groups: {
    type: Array,
    required: true
  },
  leftovers: {
    type: Array,
    default: () => []
  }
});

const copied = ref(false);

const copyResults = () => {
  let text = 'GRUPOS GENERADOS\n=================\n\n';
  props.groups.forEach((group, idx) => {
    text += `${group.name}\n`;
    if (group.topic) {
      text += `Tema: ${group.topic}\n`;
    }
    text += 'Miembros:\n';
    group.students.forEach((student, sIdx) => {
      text += ` - ${student}\n`;
    });
    text += '\n';
  });

  if (props.leftovers && props.leftovers.length > 0) {
    text += 'Estudiantes reasignados equitativamente:\n';
    text += ` - ${props.leftovers.join(', ')}\n\n`;
  }

  navigator.clipboard.writeText(text).then(() => {
    copied.value = true;
    setTimeout(() => {
      copied.value = false;
    }, 2000);
  });
};

const printResults = () => {
  window.print();
};
</script>

<style scoped>
@media print {
  /* Estilos específicos para impresión */
  #resultados-sorteo {
    background: white;
    color: black;
  }
  button, [id^="adsense-"] {
    display: none !important;
  }
  .grid {
    display: block !important;
  }
  .bg-white {
    border: 1px solid #ccc !important;
    margin-bottom: 20px !important;
    page-break-inside: avoid;
  }
}
</style>
