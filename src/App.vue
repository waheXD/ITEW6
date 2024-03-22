<template>
  <div id="app">
    <Shopping :cart="cart" @add-to-cart="addToCart" @remove-from-cart="removeFromCart"
      @remove-product="removeProduct" />
  </div>
</template>

<script>
import Shopping from './components/shoppingCart.vue';

export default {
  name: 'App',
  components: {
    Shopping
  },
  data() {
    return {
      cart: []
    };
  },
  methods: {
    addToCart(item) {
      let existingItem = this.cart.find(i => i.id === item.id);
      if (existingItem) {
        existingItem.quantity++;
      } else {

        this.cart.push(item);
      }
    },
    removeFromCart(index) {
      this.cart.splice(index, 1);
    },
    removeProduct(index) {
      this.products.splice(index, 1);
    }
  }
};
</script>

<style></style>