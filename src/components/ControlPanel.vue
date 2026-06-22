<template>
  <div class="bg-white p-6 rounded-2xl shadow-sm border border-[#9BB080]/30 flex flex-col gap-6 w-full">
    <h2 class="text-xl font-semibold text-[#2B3E42] flex items-center gap-2">
      <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-[#609966]" fill="none" viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6V4m0 2a2 2 0 100 4m0-4a2 2 0 110 4m-6 8a2 2 0 100-4m0 4a2 2 0 110-4m0 4v2m0-6V4m6 6v10m6-2a2 2 0 100-4m0 4a2 2 0 110-4m0 4v2m0-6V4" />
      </svg>
      Configuración del Sorteo
    </h2>

    <!-- Toggle Selector -->
    <div class="flex flex-col gap-2">
      <label class="text-xs font-bold text-[#2B3E42]/70 uppercase tracking-wide">Criterio de Agrupación</label>
      <div class="grid grid-cols-2 gap-2 bg-[#F6F7F3] p-1.5 rounded-xl border border-[#9BB080]/20">
        <button
          type="button"
          @click="setMode('groups')"
          :class="[
            'py-2 px-3 text-sm font-medium rounded-lg transition-all duration-300 flex items-center justify-center gap-1.5',
            mode === 'groups' 
              ? 'bg-[#9BB080] text-white shadow-sm' 
              : 'text-[#2B3E42]/80 hover:bg-[#E9EDE4] hover:text-[#2B3E42]'
          ]"
        >
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 11-4 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z" />
          </svg>
          Total de Grupos
        </button>
        <button
          type="button"
          @click="setMode('students')"
          :class="[
            'py-2 px-3 text-sm font-medium rounded-lg transition-all duration-300 flex items-center justify-center gap-1.5',
            mode === 'students' 
              ? 'bg-[#9BB080] text-white shadow-sm' 
              : 'text-[#2B3E42]/80 hover:bg-[#E9EDE4] hover:text-[#2B3E42]'
          ]"
        >
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z" />
          </svg>
          Alumnos por Grupo
        </button>
      </div>
    </div>

    <!-- Numeric Input -->
    <div class="flex flex-col gap-2">
      <label for="group-qty-input" class="text-xs font-bold text-[#2B3E42]/70 uppercase tracking-wide">
        {{ mode === 'groups' ? '¿Cuántos grupos quieres formar?' : '¿Cuántos estudiantes por grupo?' }}
      </label>
      <div class="relative flex items-center">
        <button 
          type="button"
          @click="decrement"
          class="absolute left-2 w-8 h-8 rounded-lg bg-[#F6F7F3] hover:bg-[#E9EDE4] flex items-center justify-center text-[#2B3E42] font-bold text-lg select-none transition-colors border border-[#9BB080]/30"
        >
          −
        </button>
        <input
          id="group-qty-input"
          type="number"
          v-model.number="quantity"
          min="1"
          class="w-full text-center py-2 px-12 rounded-xl border border-[#9BB080]/40 focus:border-[#609966] focus:ring-2 focus:ring-[#609966]/20 outline-none transition-all duration-300 font-bold text-lg text-[#2B3E42] bg-[#F6F7F3]/30 [appearance:textfield] [&::-webkit-outer-spin-button]:appearance-none [&::-webkit-inner-spin-button]:appearance-none"
        />
        <button 
          type="button"
          @click="increment"
          class="absolute right-2 w-8 h-8 rounded-lg bg-[#F6F7F3] hover:bg-[#E9EDE4] flex items-center justify-center text-[#2B3E42] font-bold text-lg select-none transition-colors border border-[#9BB080]/30"
        >
          +
        </button>
      </div>
    </div>

    <!-- Generate Button -->
    <button
      type="button"
      @click="emitGenerate"
      :disabled="isDisabled"
      class="w-full py-4 px-6 rounded-xl font-bold text-white shadow-md transition-all duration-300 flex items-center justify-center gap-2 select-none"
      :class="[
        isDisabled
          ? 'bg-[#E9EDE4] text-[#2B3E42]/40 cursor-not-allowed border border-[#9BB080]/20'
          : 'bg-[#609966] hover:bg-[#385E3C] hover:shadow-lg active:scale-[0.98]'
      ]"
    >
      <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 animate-pulse" fill="none" viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19.428 15.428a2 2 0 00-1.022-.547l-2.387-.477a6 6 0 00-3.86.517l-.318.158a6 6 0 01-3.86.517L6.05 15.21a2 2 0 00-1.806.547M8 4h8l-1 1v5.172a2 2 0 00.586 1.414l5 5c1.26 1.26.367 3.414-1.415 3.414H4.828c-1.782 0-2.674-2.154-1.414-3.414l5-5A2 2 0 009 10.172V5L8 4z" />
      </svg>
      Generar Grupos
    </button>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const props = defineProps({
  studentCount: {
    type: Number,
    required: true
  }
});

const mode = ref('groups'); // 'groups' | 'students'
const quantity = ref(4);

const setMode = (newMode) => {
  mode.value = newMode;
  // Ajuste inteligente al cambiar de modo
  if (newMode === 'students') {
    quantity.value = Math.max(2, Math.min(5, Math.ceil(props.studentCount / 4)));
  } else {
    quantity.value = Math.max(2, Math.min(10, Math.ceil(props.studentCount / 4)));
  }
};

const increment = () => {
  quantity.value = (quantity.value || 0) + 1;
};

const decrement = () => {
  if (quantity.value > 1) {
    quantity.value = quantity.value - 1;
  }
};

const isDisabled = computed(() => {
  return props.studentCount === 0 || quantity.value < 1;
});

const emit = defineEmits(['generate']);

const emitGenerate = () => {
  if (isDisabled.value) return;
  emit('generate', {
    mode: mode.value,
    quantity: quantity.value
  });
};
</script>
