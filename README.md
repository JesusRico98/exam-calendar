# 📅 Calendario Mensual en Vue

Este proyecto es una aplicación **WEB/Escritorio en Vue 3** que genera un calendario mensual personalizado con reglas de colores para días laborales, fines de semana, festivos y días fuera de rango.

## 🚀 Requisitos previos

Antes de comenzar asegúrate de tener instalado en tu máquina:

- [Node.js](https://nodejs.org/) (versión recomendada: 18 o superior)
- [Git](https://git-scm.com/)
- Un gestor de paquetes como **npm** (incluido con Node.js) o **yarn**

## 📥 Instalación

1. **Clonar el repositorio:**

   ```bash
   git clone https://github.com/JesusRico98/exam-calendar.git
   ```

2. **Entrar al directorio del proyecto:**

   ```bash
   cd calendario-vue
   ```

3. **Instalar dependencias:**

   ```bash
   npm install
   ```

   ó si usas yarn:

   ```bash
   yarn install
   ```

## ▶️ Uso en desarrollo

Ejecuta el servidor de desarrollo:

```bash
npm run dev
```

Esto iniciará la aplicación y normalmente estará disponible en: 👉 [http://localhost:5173](http://localhost:5173)

## 🧩 Estructura del proyecto

- `src/components/CalendarioMensual.vue` → Componente principal del calendario
- `src/App.vue` → Punto de montaje de la aplicación
- `vite.config.js` → Configuración de Vite

###

## 🎨 Características principales

- Captura de **fecha de inicio** y **número de días** a mostrar.
- Generación automática de meses y semanas necesarios.
- Encabezado por mes con nombre y días de la semana.
- Colores personalizados:
  - ✅ Lunes a viernes: **verde**
  - 🟨 Sábado y domingo: **amarillo**
  - 🟧 Días festivos: **naranja**
  - ⬜ Días fuera de rango o de otro mes: **gris**
- Festivos incluidos:
  - 1 de enero
  - 5 de febrero
  - 18 de marzo
  - 1 de mayo
  - 16 de septiembre
  - 20 de noviembre
  - 25 de diciembre

##
