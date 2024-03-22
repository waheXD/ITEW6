<template>
  <div class="shopping-cart">
    <h1>Shopping Cart</h1><br>

    <form @submit.prevent="addNewProduct">
      <label for="productName">Product Name: </label>
      <br>
      <input type="text" id="productName" v-model="newProduct.name" required><br>
      <label for="productPrice"> Price: </label>
      <br>
      <input type="number-Price" id="productPrice" v-model.number="newProduct.price" min="0" step="0.01" required>
      <br><br>
      <button type="submit">Add Product</button>
    </form>
    <hr>
    <br>
    <div class="product-list">
      <div v-for="product in products" :key="product.id" class="product">
        <div v-if="product.id !== editingProductId">
          <img src="./assets/a.jpg" alt="PRODUCT PICTURE">
          <p>{{ product.name }} - ₱ {{ product.price }}</p>
          Qnty: <input type="number" v-model="product.quantityToAdd" min="1" step="1">
          <button @click="editProduct(product)">Edit</button>
          <button @click="addToCart(product, product.quantityToAdd)">Add to Cart</button>
          <button @click="removeProduct(product.id)">Remove</button>
        </div>

        <div v-else class="edit-form">
          Product Name: <input type="text-edit" v-model="editedProduct.name"><br>
          Price: <br><input type="number" v-model.number="editedProduct.price" min="1" step="0.01">
          <br>
          <button @click="saveEdit">Save</button>
          <button @click="cancelEdit">Cancel</button>
        </div>
      </div>
    </div>

    <h2>Cart</h2>
    <div class="cart">
      <div v-for="(item, index) in cart" :key="index" class="cart-item">
        <p>{{ item.name }} - ₱{{ item.price }} - Quantity: {{ item.quantity }}</p>
        <input type="number" v-model="item.quantity" min="1" @change="updateQuantity(index, item.quantity)">
        <button @click="removeFromCart(index)">Remove</button>
      </div>
      <p class="total">Total: ₱{{ totalPrice }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [],
      newProduct: {
        name: '',
        price: null
      },
      editingProductId: null,
      editedProduct: {
        id: null,
        name: '',
        price: null
      }
    };
  },

  watch: {
    products: {
      deep: true,
      handler(newProducts) {
        this.cart.forEach(cartItem => {
          const correspondingProduct = newProducts.find(product => product.id === cartItem.id);
          if (correspondingProduct) {
            cartItem.price = correspondingProduct.price;
          }
        });
      }
    }
  },

  props: ['cart'],
  computed: {
    totalPrice() {
      return this.cart.reduce((total, item) => total + (item.price * item.quantity), 0);
    }
  },
  methods: {
    addToCart(product, quantity) {
      let existingItem = this.cart.find(item => item.id === product.id);
      if (existingItem) {
        existingItem.quantity += quantity;
      } else {
        this.$emit('add-to-cart', { id: product.id, name: product.name, price: product.price, quantity });
      }
    },
    removeProduct(id) {
      const index = this.products.findIndex(product => product.id === id);
      if (index !== -1) {
        this.products.splice(index, 1);
      }
    },
    removeFromCart(index) {
      this.$emit('remove-from-cart', index);
    },
    updateQuantity(index, quantity) {
      if (quantity < 1) {
        this.$emit('remove-from-cart', index);
      }
    },
    addNewProduct() {
      if (this.newProduct.name && this.newProduct.price) {
        this.products.push({
          id: this.products.length + 1,
          name: this.newProduct.name,
          price: parseFloat(this.newProduct.price),
          quantityToAdd: 1
        });
        this.newProduct.name = '';
        this.newProduct.price = null;
      }
    },
    editProduct(product) {
      this.editingProductId = product.id;
      this.editedProduct.id = product.id;
      this.editedProduct.name = product.name;
      this.editedProduct.price = product.price;
    },
    saveEdit() {
      const index = this.products.findIndex(product => product.id === this.editedProduct.id);
      if (index !== -1) {
        this.products[index].name = this.editedProduct.name;
        this.products[index].price = parseFloat(this.editedProduct.price);
        this.editingProductId = null;
        this.editedProduct.id = null;
        this.editedProduct.name = '';
        this.editedProduct.price = null;
      }
    },
    cancelEdit() {
      this.editingProductId = null;
      this.editedProduct.id = null;
      this.editedProduct.name = '';
      this.editedProduct.price = null;
    }
  }
};
</script>
<style>
button {
  padding: 10px;
  background-color: antiquewhite;
}

input {
  padding: 10px;
}
</style>