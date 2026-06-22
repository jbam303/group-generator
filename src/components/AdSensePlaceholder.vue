<template>
  <!-- Modo desarrollador / Fallback (si no hay cliente de AdSense configurado) -->
  <div 
    v-if="!adsenseClient"
    :class="[
      'bg-[#E9EDE4] border-2 border-dashed border-[#9BB080]/60 rounded-xl flex flex-col items-center justify-center text-center p-4 transition-all duration-300 hover:border-[#609966]/80 my-4 select-none print:hidden',
      typeClass
    ]"
    :id="`adsense-placeholder-${type}`"
  >
    <div class="text-[10px] uppercase tracking-widest text-[#2B3E42]/50 font-bold mb-1">Publicidad / Google AdSense (Placeholder)</div>
    <div class="text-[#2B3E42] font-semibold text-sm flex items-center gap-2">
      <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-[#609966]" fill="none" viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
      </svg>
      {{ label }}
    </div>
    <div class="text-xs text-[#2B3E42]/60 mt-1 italic">{{ sizeLabel }}</div>
  </div>

  <!-- Modo producción (si hay cliente de AdSense configurado) -->
  <div 
    v-else
    :class="['my-4 overflow-hidden flex justify-center items-center w-full print:hidden', typeClass]"
    :id="`adsense-unit-${type}`"
  >
    <ins 
      class="adsbygoogle"
      :style="adStyle"
      :data-ad-client="adsenseClient"
      :data-ad-slot="adSlot"
      :data-ad-format="adFormat"
      :data-full-width-responsive="fullWidth"
    ></ins>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const props = defineProps({
  type: {
    type: String,
    required: true, // 'top', 'sidebar', 'native'
  }
});

// Detectar variables de entorno de Vite o usar fallback en producción
const adsenseClient = ref(import.meta.env.VITE_ADSENSE_CLIENT || (import.meta.env.PROD ? 'ca-pub-6156944486787742' : ''));

const adSlot = computed(() => {
  if (props.type === 'top') return import.meta.env.VITE_ADSENSE_SLOT_TOP || '';
  if (props.type === 'sidebar') return import.meta.env.VITE_ADSENSE_SLOT_SIDEBAR || '';
  if (props.type === 'native') return import.meta.env.VITE_ADSENSE_SLOT_NATIVE || '';
  return '';
});

// Estilos de AdSense para producción
const adStyle = computed(() => {
  switch (props.type) {
    case 'top': return { display: 'inline-block', width: '100%', height: '90px' };
    case 'sidebar': return { display: 'block', width: '100%', height: '600px' };
    case 'native': return { display: 'block' };
    default: return { display: 'block' };
  }
});

const adFormat = computed(() => {
  if (props.type === 'native') return 'fluid';
  return 'auto';
});

const fullWidth = computed(() => {
  return props.type === 'top' || props.type === 'native' ? 'true' : 'false';
});

// Contenido para el modo Placeholder
const label = computed(() => {
  switch (props.type) {
    case 'top': return 'Banner Superior Horizontal';
    case 'sidebar': return 'Panel Lateral Publicitario';
    case 'native': return 'Anuncio Nativo Integrado';
    default: return 'Bloque de Anuncios';
  }
});

const sizeLabel = computed(() => {
  switch (props.type) {
    case 'top': return '728 × 90 px (Leaderboard)';
    case 'sidebar': return '300 × 600 px (Skyscraper)';
    case 'native': return 'Formatos recomendados (In-feed / Multiplex)';
    default: return 'Adaptable';
  }
});

const typeClass = computed(() => {
  switch (props.type) {
    case 'top': return 'w-full h-[90px]';
    case 'sidebar': return 'w-full md:w-[300px] h-[450px] md:h-[600px] sticky top-4';
    case 'native': return 'w-full min-h-[120px]';
    default: return 'w-full h-auto';
  }
});

onMounted(() => {
  if (adsenseClient.value && adSlot.value) {
    try {
      (window.adsbygoogle = window.adsbygoogle || []).push({});
    } catch (e) {
      console.warn("Google AdSense: Error al inicializar unidad de anuncios", e);
    }
  }
});
</script>
