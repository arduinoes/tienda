---
sidebar: auto
---

# Cesta

## views > Cart.vue

### Importaciones
1. Modulos de vue
2. Componentes CardCard + esqueleto
3. Utilidad moneda
4. Store / pinia

### Funciones
1. **formattedCart()** devuelve el array de la cesta
    - stores > cart.ts
2. **loaded()** comprueba la carga de productos
    - stores > products.ts
3. **length()** comprueba si hay productos en la cesta
    - stores > cart.ts
4. **total()** acumula / sumatorio => precios * cantidad   
    - stores > cart.ts
```vue
<script setup lang="ts">
import { computed } from 'vue';
import CartCard from '../components/CartCard.vue';
import CartCardSkeleton from '../components/CartCardSkeleton.vue';
import { toCurrency } from '../shared/utils';
import { useCartStore } from '../stores/cart';
import { useProductStore } from '../stores/products';

const cartStore = useCartStore();
const productStore = useProductStore();

const formattedCart = computed(() => cartStore.formattedCart);
</script>

<template>
  <div class="p-4 max-w-4xl mx-auto">
    <div v-if="!productStore.loaded" class="space-y-4">
      <CartCardSkeleton v-for="n in 15" :key="n" />
    </div>
    <div v-else-if="!formattedCart.length">
      <h1 class="text-xl">Cesta vacía.</h1>
    </div>
    <div v-else class="space-y-4">
      <CartCard
        v-for="(cartProduct, index) in formattedCart"
        :key="index"
        :cartProduct="cartProduct"
      />
      <div class="text-right text-2xl md:text-4xl">
        Total: {{ toCurrency(cartStore.total) }}
      </div>
    </div>
  </div>
</template>
```
#### Views HomeView

HomeView.vue

```vue
<template>
  <h1>{{ infoApp.nombre }}</h1>
  <h1>{{ infoApp.texto }}</h1>
  <Contador
    :limite="10"
    :alerta-limite-mensajes='"No puede superar"'
  />
</template>
```

## components > CartCard.vue

### Importaciones
1. Stores / pinia
2. Utilidad moneda
### Funciones
1. **router-link** a producto
2. **remove() / add()** añadir y quitar
    - stores > cart.ts
```vue
<script setup lang="ts">
import { useCartStore } from '../stores/cart';
import type { CartPreview } from '../stores/cart'
import { toCurrency } from '../shared/utils'

const cartStore = useCartStore()

defineProps<{
    cartProduct: CartPreview
}>();
</script>

<template>
  <div class="card md:card-side bordered">
    <figure class="p-8">
      <img
        :src="cartProduct.image"
        alt="Card Image"
        class="object-contain w-full h-48"
      />
    </figure>
    <div class="card-body">
      <h2 class="card-title">
        <router-link
          class="link link-hover"
          :to="`/product/${cartProduct.id}`"
          >{{ cartProduct.title }}</router-link
        >
      </h2>
      <p>{{ toCurrency(cartProduct.cost) }}</p>
      <div class="card-actions">
        <div class="btn-group">
          <button
            class="btn btn-primary"
            @click="cartStore.remove(cartProduct.id)"
          >
            -
          </button>
          <button class="btn btn-ghost no-animation">
            {{ cartProduct.quantity }}
          </button>
          <button
            class="btn btn-primary"
            @click="cartStore.add(cartProduct.id)"
          >
            +
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
```