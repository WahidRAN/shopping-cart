<script setup>
</script>

<template>
  <header>
    <h1>Shopping Cart App</h1>
  </header>

  <main>
    <div id="cards-group" class="cards-container">

    </div>
    <div id="shopping-cart" class="cart-container">
      <section class="card-cart">
        <div class="cart-title">
          <h2>Shopping Cart</h2>
        </div>
        <div class="cart-items">
          <h2>Something</h2>
          <h2>Something</h2>
          <!-- <h2>Something</h2>
          <h2>Something</h2>
          <h2>Something</h2>
          <h2>Something</h2>
          <h2>Something</h2>
          <h2>Something</h2>
          <h2>Something</h2>
          <h2>Something</h2>
          <h2>Something</h2> -->
        </div>
        <hr>
        <div class="cart-total">
          <p id="label-total">Total</p>
          <p id="cart-total">Rp. 15.000.000</p>
        </div>
        <button class="btn-primary"><span class="btn-label">Checkout</span></button>
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

main {
  display: flex;
  margin: 0 2rem;
}

.cards-container {
  flex-grow: 5;
  min-height: 90vh;
  background-color: aqua;
}

.cart-container {
  flex-grow: 1;
  max-height: 65vh;
  padding: 1rem;
}

.card-cart {
  display: flex;
  flex-direction: column;
  /* justify-content: space-between; */
  /* min-height: 100%; */
  box-shadow: var(--effect-box-shadow);
  border-radius: var(--border-radius-md);
  background-color: var(--vt-c-white);
  padding: 2rem;
}

.cart-title {
  margin-bottom: 1rem;
  font-family: 'Poppins';
  font-weight: 600;
  color: var(--color-heading);
}

.cart-items {
  /* min-height: 10vh; */
  background-color: aqua;
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
  font-family: 'Mulish', sans-serif;
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
