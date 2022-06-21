---
sidebar: auto
---
# Pinia

## Stores

### store > products.ts

```js
import { defineStore } from 'pinia';
import { collection, query, getDocs, orderBy } from "firebase/firestore";
import { db } from "../firebase";

export interface Product {
    id: string;
    title: string;
    price: number;
    description: string;
    category: string;
    image: string;
  }
interface ProductState {
  items: Record<string, Product>;
  ids: string[];
  productos: [];
  total: number;
}

export const useProductStore = defineStore({
  id: 'products',

  state: (): ProductState => ({
    items: {},
    ids: [],
    productos: [],
    total: 0
  }),
  getters: {
    list(): Product[] {
      return this.ids.map((i) => this.items[i]);
    },
    loaded(): boolean {
      return this.ids.length > 0;
    },
  },
  actions: {
    async obtenerDatos() {
      const first = query(collection(db, "productos"), orderBy("title"));
      this.productos = [];
      const querySnapshot = await getDocs(first);
      querySnapshot.forEach((doc) => {
        
        let producto = doc.data();
        producto.id = doc.id;
        this.total = this.productos.push(producto);
        console.log(this.total)
      });
      console.log("Este es el array productos", this.productos)
      const data: Product[] = this.productos;
      this.ids = data.map((product) => {
      this.items[product.id] = product ;
      return product.id;
      });
      console.log("Este es el array ids", this.ids)
      console.log("Este es el objeto items", this.items)
    },
  },
});
```

### store > cart.ts

```js
import { defineStore } from 'pinia';
import { CART_STORAGE } from '../composables/usePersistCart';
import { useProductStore } from './products';

// Tipos de datos
export interface Purchase {
  productId: string;
  quantity: number;
}
// Tipo de datos
interface CartState {
  contents: Record<string, Purchase>;
}
// Tipo de datos
export interface CartPreview {
  id: string;
  image: string;
  title: string;
  quantity: number;
  cost: number;
}

export const useCartStore = defineStore({
  id: 'cart',

  state: (): CartState => ({
    
    contents: JSON.parse(localStorage.getItem(CART_STORAGE) as string) ?? {},
  }),

  getters: {
    count(): number {                         // acumulado
      return Object.keys(this.contents).reduce((acc, id) => {
        return acc + this.contents[id].quantity;
      }, 0);
    },

    total(): number {
      const products = useProductStore();
      return Object.keys(this.contents).reduce((acc, id) => {
        return acc + products.items[id].price * this.contents[id].quantity;
      }, 0);
    },

    formattedCart(): CartPreview[] {
      const products = useProductStore();

      if (!products.loaded) return [];

      return Object.keys(this.contents).map((productId) => {
        const purchase = this.contents[productId];

        return {
          id: purchase.productId,
          image: products.items[purchase.productId].image,
          title: products.items[purchase.productId].title,
          quantity: purchase.quantity,
          cost: purchase.quantity * products.items[purchase.productId].price,
        };
      });
    },
  },

  actions: {
    add(productId: string) {
      if (this.contents[productId]) {
        this.contents[productId].quantity += 1;
      } else {
        this.contents[productId] = {
          productId,
          quantity: 1,
        };
      }
    },
    remove(productId: string) {
      if (!this.contents[productId]) {
        return;
      }

      this.contents[productId].quantity -= 1;

      if (this.contents[productId].quantity === 0) {
        delete this.contents[productId];
      }
    },
  },
});
```
