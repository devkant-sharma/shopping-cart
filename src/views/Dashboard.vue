<template>
  <div>
    <div class="top-nav">
      <div></div>
      <div class="top-nav__title">
        Product Catalog
      </div>
      <button class="btn btn-primary">({{ itemsInCart }}) Cart</button>
    </div>
    <div class="container">
      <div class="product-details">
        <div class="headline-2">Products</div>

        <div class="items-container">
          <div class="item" v-for="product in products" :key="product.id">
            <div class="item__img">
              <img
                :src="require(`../assets/${product.img}.jpg`)"
                alt="itemimg"
              />
            </div>

            <div class="item__title headline-3">{{ product.title }}</div>

            <div class="item__price">Rs. {{ product.price }}</div>

            <div class="item__desc">
              {{ product.desc | desc }}
            </div>

            <div class="item__action">
              <button
                :disabled="
                  product.selectedQuantity <= 0 || product.quantity === 0
                "
                class="btn btn-primary"
                @click="addToCart(product)"
              >
                Add to cart
              </button>
              <input
                :min="0"
                :max="product.quantity"
                v-model.number="product.selectedQuantity"
                type="number"
                class="form-input"
              />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import productDetails from "@/static/product-details.js";

function getProducts() {
  if (localStorage.getItem("products")) {
    return JSON.parse(localStorage.getItem("products"));
  }

  localStorage.setItem("products", JSON.stringify(productDetails.products));
  return productDetails.products;
}

export default {
  name: "Dashboard",

  data() {
    return {
      products: getProducts(),
      cart: JSON.parse(localStorage.getItem("cart")) || [],
    };
  },

  methods: {
    addToCart(val) {
      // add to cart
      const item = { ...val }; // deep copy
      const { id } = item;
      const found = this.cart.find((f) => f.id === id);
      if (found) {
        this.cart = this.cart.map((c) => {
          if (c.id === id) {
            c.selectedQuantity = c.selectedQuantity + item.selectedQuantity;
          }
          return c;
        });
      } else {
        this.cart.push(item);
      }

      // update products
      this.products = this.products.map((p) => {
        if (p.id === item.id) {
          p.quantity = p.quantity - item.selectedQuantity;
        }
        p.selectedQuantity = 0;
        return p;
      });
      this.updateProductsLS(this.products);
      this.updateCartLS(this.cart);
    },

    updateProductsLS(data) {
      localStorage.setItem("products", JSON.stringify(data));
    },

    updateCartLS(data) {
      localStorage.setItem("cart", JSON.stringify(data));
    },
  },

  computed: {
    itemsInCart() {
      let count = 0;
      this.cart.forEach((c) => {
        count += c.selectedQuantity;
      });
      return count;
    },
  },

  filters: {
    desc: function(value) {
      return value.substr(0, 100);
    },
  },
};
</script>
