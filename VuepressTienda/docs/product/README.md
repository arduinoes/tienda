---
sidebar: auto
---

# Ruta dinámica 

## views > Product.vue

### Importaciones
1. Computed de vue
2. Rutas / router
3. Stores card + products
3. Tipos typescript / interface
5. Utilidad de moneda
6. Componente esqueleto
### Funciones
1. Buscamos en **items** por **producto.Id**
    - stores > product
2. Añadimos la función **add**  
    - stores > card

```vue
<script setup lang="ts">
import { computed } from 'vue';
import { useRoute } from 'vue-router';
import { useCartStore } from '../stores/cart';
import { useProductStore } from '../stores/products';
import type { Product } from '../stores/products';
import { toCurrency } from '../shared/utils';
import CartCardSkeleton from '../components/CartCardSkeleton.vue';

const cartStore = useCartStore();
const productStore = useProductStore();

const route = useRoute();

const product = computed<Product>(
  () => productStore.items[route.params.productId as string]
);
</script>
```

## Componentes > Navbar
### Importaciones 
1. Computed de vue
2. Stores cart / cesta
3. Componente Search.vue

### Funciones
1. Contador de productos
2. router-link
3. Componente Search.vue

```vue
<script setup lang="ts">
import { computed } from 'vue';
import { useCartStore } from '../stores/cart';
import Search from './Search.vue';

const cartStore = useCartStore();

const count = computed(() => cartStore.count);
</script>
<template>
  <div class="navbar mb-2 shadow-lg bg-neutral text-neutral-content">
    <div class="flex-none lg:hidden">
      <label for="drawer-input" class="btn btn-square btn-ghost">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          class="inline-block w-6 h-6 stroke-current"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M4 6h16M4 12h16M4 18h16"
          ></path>
        </svg>
      </label>
    </div>
    <div class="flex-none px-2 mx-2">
      <span class="text-lg font-bold"> Arduinoes Tienda</span>
    </div>
    <div class="flex-1 px-2 mx-2">
      <div class="items-stretch hidden lg:flex">
        <router-link class="btn btn-ghost btn-sm rounded-btn" to="/">
          Productos
        </router-link>
        <router-link class="btn btn-ghost btn-sm rounded-btn" to="/cart">
          Cesta
          <div class="badge ml-2 badge-outline" v-text="count"></div>
        </router-link>
      </div>
    </div>
    <div class="flex-1 lg:flex-none">
      <Search />
    </div>
  </div>
</template>
```
## Components > Search.vue
### Importaciones 
1. Importamos modulos de vue, 
2. Router
3. Store / pinia

### Funciones
1. Búsqueda a través de filter
3. Navegación por ruta dinámica

```vue
<script setup lang="ts">
import { computed, ref } from 'vue';
import { useRouter } from 'vue-router';
import { useProductStore } from '../stores/products';

const productStore = useProductStore();
const router = useRouter();
const input = ref('');
const searchResults = computed(() => {
  if (!input.value || input.value.length < 2) return [];
  return productStore.list.filter((item) => {   // Valor en minúsculas
    return item.title.toLowerCase().includes(input.value.toLowerCase());
  });
});

const navigate = (id: string) => {
  input.value = '';
  router.push(`/product/${id}`);
};
</script>

<template>
  <div class="dropdown dropdown-end">
    <div class="form-control">
      <input
        type="text"
        placeholder="Buscar..."
        class="input input-ghost"
        :disabled="!productStore.loaded"
        v-model="input"
      />
    </div>
    <ul
      class="shadow menu dropdown-content bg-base-100 rounded-box w-64 text-base-content overflow-y-scroll"
      style="max-height: 50vh"
    >
      <li v-for="product in searchResults" :key="product.id">
        <a
          href="#"
          @click.prevent="navigate(product.id)"
          v-text="product.title"
        ></a>
      </li>
    </ul>
  </div>
</template>
```