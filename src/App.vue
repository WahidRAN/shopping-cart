<script>
import { reactive } from "vue";

export default {
  setup(_ctx, _props) {
    const state = reactive({
      data: null,
      cart: [],
      cartQTY: 0,
      cartSubtotal: 0,
      cartTotal: 0,
    });

    var requestOptions = {
      method: "GET",
      redirect: "follow",
    };

    const useFetch = () => {
      return fetch("https://dummyjson.com/products", requestOptions)
        .then((response) => response.json())
        .then((result) => {
          state.data = result.products;
          return state.data;
        })
        .catch((error) => console.log("error", error));
    };

    useFetch();

    const formatIDR = (value) => {
      const formatter = new Intl.NumberFormat("id-ID", {
        style: "currency",
        currency: "IDR",
        maximumFractionDigits: 0,
      });
      return formatter.format(value);
    };

    const discountPrice = (price, discount) => {
      const priceInRupiah = price * 15000;
      const discountRound = Math.round(discount);

      const cutOff = (discountRound / 100) * priceInRupiah;
      return priceInRupiah - cutOff;
    };

    const sellPrice = (price, discount) => {
      const priceAfterDiscount = discountPrice(price, discount);
      return formatIDR(priceAfterDiscount);
    };

    const addToCart = (data) => {
      const itemPrice = discountPrice(data.price, data.discountPercentage);
      const itemIndex = state.cart.findIndex((e) => e.id === data.id);

      if (state.cart.some((e) => e.id === data.id)) {
        const cartItem = state.cart[itemIndex]
        state.cartTotal += cartItem.subtotal;
        cartItem.qty++;
        cartItem.subtotal += itemPrice;
        console.log('subtotal', cartItem.subtotal);
        console.log('total', state.cartTotal);
      } else {
        state.cartQTY++;
        state.cartSubtotal += itemPrice;

        const itemCart = {
          id: data.id,
          name: data.title,
          qty: state.cartQTY,
          price: itemPrice,
          subtotal: state.cartSubtotal,
        };

        state.cartTotal += state.cartSubtotal;

        console.log('subtotal', state.cartSubtotal);
        console.log('total', state.cartTotal);

        state.cart.push(itemCart);
        state.cartQTY = 0;
        state.cartSubtotal = 0;
      }
    };

    const removeFromCart = (data) => {
      const itemIndex = state.cart.findIndex((e) => e.id === data.id);
      return state.cart.splice(itemIndex, 1);
    };

    return {
      state,
      addToCart,
      discountPrice,
      formatIDR,
      removeFromCart,
      sellPrice,
    };
  },
};
</script>

<template>
  <header>
    <h1>Shopping Cart App</h1>
  </header>

  <main>
    <!-- <pre>{{ state.cartTotal }}</pre> -->
    <div id="cards-group" class="cards-container">
      <section v-for="data in state.data" :key="data.id" class="card-item">
        <img :src="data.thumbnail" alt="image" class="item-image" />
        <h3 class="item-category">{{ data.category }}</h3>
        <div class="item-spec">
          <h4 class="item-title">{{ data.title }}</h4>
          <p class="item-desc">{{ data.description }}</p>
          <p class="item-price">
            {{ sellPrice(data.price, data.discountPercentage) }}
          </p>
          <section class="discount">
            <div class="discount-percentage">
              {{ Math.round(data.discountPercentage) }}%
            </div>
            <p class="normal-price">{{ formatIDR(data.price * 15000) }}</p>
          </section>
          <p class="rating">{{ data.rating }}</p>
        </div>
        <div class="item-footer">
          <button class="btn-item btn-primary" @click="addToCart(data)">
            <span class="btn-label btn-label-item">Add to Cart</span>
          </button>
        </div>
      </section>
    </div>
    <div id="shopping-cart" class="cart-container">
      <section v-if="state.cart && state.cart.length > 0" class="card-cart">
        <div class="cart-title">
          <h2>Shopping Cart</h2>
        </div>
        <div class="cart-items">
          <section class="item-header">
            <h2 class="header-label">Name</h2>
            <h2 class="header-label">QTY</h2>
            <h2 class="header-label">Price</h2>
            <h2 class="header-label">Sub total</h2>
          </section>
          <section v-for="cart in state.cart" :key="cart.id" class="items">
            <h5 class="items-label">{{ cart.name }}</h5>
            <h5 class="items-label">{{ cart.qty }}</h5>
            <h5 class="items-label">
              {{ formatIDR(cart.price).substring(3) }}
            </h5>
            <h5 class="items-label">
              {{ formatIDR(cart.subtotal).substring(3) }}
            </h5>
          </section>
        </div>
        <hr />
        <div class="cart-total">
          <p id="label-total">Total</p>
          <p id="cart-total">{{ formatIDR(state.cartTotal) }}</p>
        </div>
        <button class="btn-primary">
          <span class="btn-label">Checkout</span>
        </button>
      </section>
      <section v-else class="card-cart">
        <div class="cart-title">
          <h2>Shopping Cart</h2>
        </div>
        <div class="cart-content-empty">
          <img
            src="./assets/empty-cart.svg"
            alt="cart icon"
            class="empty-cart"
          />
          <h4 class="empty-label">No items in the shopping cart</h4>
        </div>
      </section>
    </div>
  </main>
</template>

<style scoped>
header {
  display: flex;
  justify-content: center;
  width: 100vw;
  min-height: 10vh;
  background-color: var(--color-primary);
}

h1 {
  text-align: center;
  margin: auto 0;
  color: var(--color-text-white);
}

h2 {
  font-family: Roboto, sans-serif;
  font-style: normal;
  font-weight: 500;
  font-size: 1.125rem;
  color: var(--color-text);
}

h5 {
  font-family: Roboto, sans-serif;
  font-style: normal;
  font-weight: 400;
  font-size: 0.875rem;
  color: var(--color-subtitle);
}

main {
  display: flex;
  /* flex-direction: column-reverse; */
  margin: 0 2rem;
}

.cards-container {
  flex-grow: 9;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;
  grid-auto-rows: minmax(400px, auto);
  padding: 1rem;
}

.card-item {
  display: flex;
  flex-direction: column;
  width: 100%;
  box-shadow: var(--effect-box-shadow);
  border-radius: var(--border-radius-md);
  background-color: var(--vt-c-white);
}

.item-image {
  border-radius: 25px 25px 0 0;
  width: 100%;
  height: 120px;
  object-fit: cover;
}

.item-category {
  border-radius: 0 0 45px 0;
  padding: 0.5rem 1rem;
  background-color: var(--color-primary);
  font-family: Poppins;
  font-style: normal;
  font-weight: 400;
  font-size: 0.625rem;
  text-transform: capitalize;
  color: var(--vt-c-white);
}

.item-spec {
  padding: 1rem;
}

.item-title {
  font-family: Roboto, sans-serif;
  font-style: normal;
  font-weight: 500;
  font-size: 0.75rem;
  line-height: 20px;
  text-transform: uppercase;
  color: var(--color-subtitle);
}

.item-desc {
  margin: 0.75rem 0;
  font-family: Roboto, sans-serif;
  font-style: normal;
  font-weight: 600;
  font-size: 0.875rem;
  line-height: 22px;
  color: var(--color-heading);
}

.item-price {
  font-family: "Poppins";
  font-style: normal;
  font-weight: 700;
  font-size: 1rem;
  line-height: 26px;
  color: var(--color-heading);
}

.discount {
  display: flex;
  align-content: center;
  margin-top: 0.5rem;
}

.discount-percentage {
  background-color: var(--vt-c-red-soft);
  border-radius: 4px;
  padding: 0.25rem;
  font-family: Roboto, sans-serif;
  font-style: normal;
  font-weight: 400;
  font-size: 0.75rem;
  /* line-height: px; */
  color: var(--color-discount);
}

.normal-price {
  margin-left: 0.5rem;
  font-family: Roboto, sans-serif;
  font-style: normal;
  font-weight: 400;
  font-size: 0.75rem;
  line-height: 28px;
  color: var(--color-subtitle);
  text-decoration: line-through;
}

.rating {
  display: flex;
  align-items: center;
  margin-top: 0.75rem;
  font-family: Roboto, sans-serif;
  font-style: normal;
  font-weight: 400;
  font-size: 0.75rem;
  line-height: 20px;
  color: var(--color-rating);
}

.rating::before {
  margin-right: 0.25rem;
  content: url(./assets/star.svg);
}

.item-footer {
  display: flex;
  justify-content: center;
  padding: 0 1rem 1rem 1rem;
}

/* Cart */
.cart-container {
  flex-grow: 0;
  max-height: 65vh;
  padding: 1rem;
}

.card-cart {
  display: flex;
  flex-direction: column;
  min-height: 368px;
  min-width: 432px;
  box-shadow: var(--effect-box-shadow);
  border-radius: var(--border-radius-md);
  background-color: var(--vt-c-white);
  padding: 2rem;
}

.cart-title {
  margin-bottom: 1rem;
  font-family: "Poppins";
  font-weight: 600;
  color: var(--color-heading);
}

.cart-items {
  display: flex;
  flex-direction: column;
}

.item-header,
.items {
  display: flex;
  flex-direction: row;
  gap: 0.25rem;
}

.item-header {
  margin-bottom: 28px;
}

.header-label,
.items-label {
  flex: 1 1 0;
  min-height: 30px;
  min-width: 72px;
}

.items {
  margin-bottom: 1rem;
}

hr {
  margin: 1rem 0;
  color: #e3e3e3;
}

.cart-total {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 0 0 1rem 0;
}

#label-total {
  font-family: Roboto, sans-serif;
  font-size: 1.125rem;
  font-weight: 400;
  line-height: 30px;
  color: var(--color-subtitle);
}

#cart-total {
  font-family: Roboto, sans-serif;
  font-size: 1.25rem;
  font-weight: 600;
  color: var(--color-heading);
}

.btn-primary {
  cursor: pointer;
  background: var(--color-primary-gradient);
  border: none;
  border-radius: 15px;
  padding: 1rem;
  color: var(--color-text-white);
  font-family: "Mulish", sans-serif;
  font-weight: 800;
  font-size: 18px;
}

.btn-label {
  display: flex;
  justify-content: center;
  align-items: center;
}

.btn-label::before {
  margin-right: 1rem;
  content: url(./assets/shopping-cart.svg);
}

.cart-content-empty {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-top: 3rem;
}

.empty-cart {
  width: 128px;
  text-align: center;
}

.empty-label {
  margin-top: 1rem;
  font-family: "Poppins";
  text-align: center;
}

.btn-item {
  width: 100%;
  margin-bottom: 0.5rem;
  padding: 0.55rem 1rem;
  font-size: 1rem;
}

.btn-label-item::before {
  margin-right: 0.5rem;
}

@media (min-width: 1024px) {
  /* header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  } */
}
</style>
