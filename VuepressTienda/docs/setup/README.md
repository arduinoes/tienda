---
sidebar: auto
---
# Configurando el proyecto

## Instalacíon

### Proyecto Vue.js 3.2
```
npm init vue@latest
```
```
Vue.js - The Progressive JavaScript Framework

✔ Project name: … vue-project
✔ Add TypeScript? … No / Yes
✔ Add JSX Support? … No / Yes
✔ Add Vue Router for Single Page Application development? … No / Yes
✔ Add Pinia for state management? … No / Yes
✔ Add Vitest for Unit Testing? … No / Yes
✔ Add Cypress for both Unit and End-to-End testing? … No / Yes
? Add ESLint for code quality? › No / Yes
```

```
npm install
```
### Firebase
```
npm i firebase
```
### Tailwind CSS
[Tailwind Vue](https://tailwindcss.com/docs/guides/vite)
```
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```
Configura tailwind.config.js
```js
module.exports = {
  content: [
    "./index.html",
    "./src/**/*.{vue,js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```
Añade el archivo src > index.css
```
@tailwind base;
@tailwind components;
@tailwind utilities;
```
### DaisyUI
[DaisyUI Instalación](https://daisyui.com/docs/install/)
```
npm i daisyui
```
Añade a tailwind.config.js
```js 
plugins: [require("daisyui")],
```
### Carpeta src

Copia y pega la carpeta src

[Proyecto](https://github.com/arduinoes/vue-tienda)

