# 🎓 Sorteador Grupal para Docentes

Una Single Page Application (SPA) moderna, limpia y rápida, diseñada específicamente para ayudar a profesores y capacitadores a conformar grupos aleatorios de alumnos y asignarles temas de disertación de forma 100% equitativa y transparente.

La aplicación se ejecuta íntegramente en el lado del cliente (client-side), garantizando la total privacidad de los datos de los estudiantes al no requerir bases de datos ni backend.

---

## ✨ Características Principales

- **Ingreso Sencillo**: Dos paneles de texto grandes para pegar o tipear la lista de estudiantes y la lista de temas (opcional).
- **Persistencia en LocalStorage**: Guarda automáticamente el estado de las áreas de texto en el navegador del usuario para prevenir cualquier pérdida de datos por recargas accidentales.
- **Modos de Agrupamiento Flexibles**:
  - Por **Cantidad total de grupos** a crear.
  - Por **Cantidad de estudiantes por grupo**.
- **Algoritmo de Aleatoriedad Robusto**: Barajado de alumnos mediante el **algoritmo de Fisher-Yates**.
- **Distribución de Sobrantes**: Si el número de alumnos no permite crear un grupo extra completo, se distribuyen equitativamente de forma individual y aleatoria en los grupos formados, mostrando una alerta visual clara de qué alumnos fueron reasignados.
- **Asignación Cíclica de Temas**: Si hay menos temas que grupos, se repiten de forma balanceada y con barajado previo para no repetir secuencias idénticas de temas.
- **Herramientas de Exportación**: Botón de copiado rápido al portapapeles en formato limpio y optimización para impresión (`Ctrl+P` / `Cmd+P`) que oculta controles y anuncios.
- **Monetización Lista**: Soporte nativo para Google AdSense con placeholders adaptables para banners (superior, lateral de escritorio e integrado en el feed de resultados).

---

## 🛠️ Tecnologías Utilizadas

- **Vue 3** (Composition API con `<script setup>`)
- **Vite** (para el bundler ultra rápido)
- **Tailwind CSS v4** (utilizando el nuevo compilador CSS optimizado)
- **Google Fonts** (`Sacramento` para el estilo manuscrito de cabecera y `Alice` para una lectura serif tipográfica de alta legibilidad)

---

## 🚀 Instalación y Desarrollo Local

Seguí estos pasos para clonar y ejecutar el generador en tu entorno de desarrollo:

### 1. Clonar el repositorio
```bash
git clone https://github.com/tu-usuario/generador-grupos-docentes.git
cd generador-grupos-docentes
```

### 2. Instalar dependencias
```bash
npm install
```

### 3. Iniciar el servidor de desarrollo
```bash
npm run dev
```
Abrí [http://localhost:5173](http://localhost:5173) en tu navegador para ver el resultado.

### 4. Compilar para producción
```bash
npm run build
```
Los archivos de distribución final se generarán en la carpeta `dist`.

---

## 💵 Configuración de Google AdSense

La aplicación incluye un sistema para inyectar publicidad real de Google AdSense de forma dinámica si se configuran las variables correspondientes. De lo contrario, se mostrarán marcadores de posición limpios y elegantes en desarrollo.

Para configurar AdSense en producción, creá un archivo `.env` en la raíz del proyecto (basado en el archivo [.env.example](.env.example)) con tus credenciales:

```env
VITE_ADSENSE_CLIENT=ca-pub-XXXXXXXXXXXXXXXX
VITE_ADSENSE_SLOT_TOP=XXXXXXXXXX
VITE_ADSENSE_SLOT_SIDEBAR=XXXXXXXXXX
VITE_ADSENSE_SLOT_NATIVE=XXXXXXXXXX
```

---

## ☁️ Despliegue en Vercel

El proyecto está 100% optimizado para desplegarse con un solo clic en **Vercel**:

1. Conectá tu repositorio de GitHub con tu cuenta de Vercel.
2. Añadí un nuevo proyecto y seleccioná el repositorio de esta aplicación.
3. Vercel detectará de manera automática que es un proyecto configurado con **Vite**.
4. (Opcional) En la sección de **Environment Variables**, podés agregar las claves de AdSense descritas arriba si vas a monetizar el sitio.
5. Presioná **Deploy** y la SPA estará en línea en segundos.
