---
sidebar: auto
---

# Home

## Archivos Base

### src > main.ts

Importamos el archivo Tailwind CSS 

```js
import './index.css'
```
### src > firebase.ts
Añadimos el archivo firebase
```js
import { initializeApp } from "firebase/app";
import { getFirestore } from 'firebase/firestore';
import { getStorage } from "firebase/storage";
import { getAuth } from "firebase/auth";

const firebaseConfig = {
  apiKey: "AIzaSyC-yjMEOwF0FAyC4Dsi3IqZcHqoZit4gyI",
  authDomain: "tiendaarduinoes.firebaseapp.com",
  projectId: "tiendaarduinoes",
  storageBucket: "tiendaarduinoes.appspot.com",
  messagingSenderId: "386838491375",
  appId: "1:386838491375:web:518d05af8440e82cb6ab1a",
  measurementId: "G-L90G5CE35V"
};

const firebaseApp = initializeApp(firebaseConfig);
const storage = getStorage(firebaseApp);
const firebase = initializeApp(firebaseConfig);
const db = getFirestore(firebase);
const auth = getAuth();

export { db, auth, storage };
```

### src > App.vue

Ejecutamos dos funciones:
1. **obtenerDatos()**, base de datos de firestore 
- **stores > products.ts**
    - forEach
    - map > ids + items 
2. **usePersistCart()**, localStorage / almacenamiento local de la cesta
- **composables > usePersistCart.ts**

```vue
<script setup lang="ts">
import Nav from './components/Nav.vue';
import { usePersistCart } from './composables/usePersistCart';
import { useProductStore } from './stores/products';

const productStore = useProductStore();
productStore.obtenerDatos();
usePersistCart();
</script>

<template>
  <Nav />
  <router-view></router-view>
</template>
```
### src > composables > usePersistCart.ts

```js
import { onUnmounted } from "vue";
import { useCartStore } from "../stores/cart";

export const CART_STORAGE = "CART_STORAGE";

export const usePersistCart = () => {
  const cartStore = useCartStore();

  const unsub = cartStore.$subscribe(() => {
    localStorage.setItem(CART_STORAGE, JSON.stringify(cartStore.contents));
  });

  onUnmounted(() => {
    unsub();
  });
};
```
### src > router > index.ts

```js
import { createRouter, createWebHistory } from "vue-router";
import Home from "../views/Home.vue";
import Product from "../views/Product.vue";
import Cart from "../views/Cart.vue";
import Formulario from "../views/Formulario.vue";

const routes = [
  { path: "/", component: Home },
  { path: "/product/:productId", component: Product },
  { path: "/cart", component: Cart },
  { path: "/formulario", component: Formulario },
];

const router = createRouter({
  history: createWebHistory(),
  routes,
});

export default router;
```

### src > views > Home.vue
Importaciones
1. Importamos **computed** de **vue**
2. Importamos 2 **componentes** 
3. Importamos la **store**
4. Ejecutamos la función **list**
    - map > return products

Componentes
1. Esqueleto
2. Tarjetas de producto
    - v-for de products
```vue
<script setup lang="ts">
import { computed } from 'vue';
import ProductCard from '../components/ProductCard.vue';
import ProductCardSkeleton from '../components/ProductCardSkeleton.vue';
import { useProductStore } from '../stores/products';

const productStore = useProductStore();

const products = computed(() => productStore.list);
</script>

<template>
  <div class="p-4 max-w-7xl mx-auto">
    <div class="grid gap-4 grid-cols-1 md:grid-cols-2 lg:grid-cols-3">
      <ProductCardSkeleton
        v-show="!productStore.loaded"
        v-for="n in 15"
        :key="n"
      />
      <ProductCard
        v-for="product in products"
        :key="product.id"
        :product="product"
      />
    </div>
  </div>
</template>
```
### src > components > ProductCard.vue

1. Importamos las stores función **add** y tipo **interface**
2. Una utilidad para el tipo/formato de moneda
3. Props de product
4. Ruta dinámica **router-link** 

```vue
<script setup lang="ts">
import { useCartStore } from '../stores/cart';
import type { Product } from '../stores/products';
import { toCurrency } from '../shared/utils';

const cartStore = useCartStore();

defineProps<{
  product: Product;
}>();
</script>

<template>
  <div class="card bordered">
    <figure class="px-8 pt-10">
      <img
        :src="product.image"
        alt="Card Image"
        class="object-contain w-full h-64"
      />
    </figure>
    <div class="card-body">
      <h2 class="card-title">
        <router-link class="link link-hover" :to="`/product/${product.id}`">{{
          product.title
        }}</router-link>
      </h2>
      <p>{{ toCurrency(product.price) }}</p>
      <div class="justify-end card-actions">
        <button class="btn btn-primary" @click="cartStore.add(product.id)">
          Añadir al carrito
        </button>
      </div>
    </div>
  </div>
</template>
```
### src > shared > utils.ts

```js
/ El objecto Intl.NumberFormat habilita el formato numérico
// de acuerdo al idioma

const formatter = new Intl.NumberFormat("es-ES", {
  style: "currency",
  currency: "EUR",
});

export const toCurrency = (value: number) => formatter.format(value);
```

