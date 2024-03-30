<template>

<nav>
    <img src="images/Favicon.png">
    <h1 v-text="sitename"></h1>
   <!--Search Bar-->
    <div class="search-bar" >
        <input type="text" placeholder="Search" v-model.trim="search" @input="customSearch" />
    </div>
   <!--Checkout Button-->
   <button class="checkout-button" v-on:click="showCheckout" v-bind:disabled="isCartEmpty">
        <span v-if="!showCheckoutPage" >
               {{ cartItemCount}}
               <span class="fas fa-cart-plus" ></span>
                    Checkout
               </span>
               <span v-else>
                    <span class="fas fa-home" ></span>
                    Home
       </span>
   </button>
   </nav>
   <main>
      <div v-if="!showCheckoutPage" >
        <Lesson/>    
      </div>

    <div v-else>
      <Checkout/>
    </div>

   </main>
    <br><br>
<footer>
    <!-- Footer content -->
    <h1 >SABA SHAIKH M00850503</h1>
</footer>

</template>

<script>
import Checkout from './components/Checkout.vue'
import Lesson from './components/Lesson.vue'

export default {
  name: 'App',
  components: {
    Checkout,
    Lesson
  }
}
</script>

<style>
</style>
