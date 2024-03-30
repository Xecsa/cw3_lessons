<template>

  
                    <!-- Shopping Cart -->
                    <div id="Checkout-cart">
                        <h2>Shopping Cart</h2>
                            <div id="cart-items" class="cart-items">
                                <!-- Cart items -->
                                <div class="cart-item" v-for="lessonId in cartLessons" :key="lessonId">
                                    <div v-for="lesson in lessons" :key="lesson.id" v-if="lesson._id === lessonId" class="lesson-card">
                                        <div class="lesson-details" >
                                            <!--Lesson image-->
                                            <div class="cart-item-image-container">
                                                <figure>
                                                    <img class="cart-item-image" v-bind:src="lesson.image" >
                                                </figure>
                                            </div>
                                            <!-- Lesson details -->
                                            <div class="lesson-icons">
                                                <p>
                                                    <i class="fa-solid fa-book"></i><span> Subject: {{ lesson.subject }}</span>
                                                </p>
                                            </div>
                                            <!-- Rating -->
                                            <div class="lesson-icons">
                                                <p>
                                                    <i class="fa-sharp fa-solid fa-location-dot"></i><span> Location: {{ lesson.location}} </span>
                                                </p>
                                            </div>
                                            <div class="lesson-icons">
                                                <p>
                                                    <i class="fas fa-dollar-sign"></i><span> Price: {{ lesson.price }} </span>
                                                </p>
                                            </div>

                                            <!--rating-->
                                            <div class="lesson-icons" ><p>
                                                <span> Rating: </span>
                                                    <span v-for="(star, index) in lesson.rating" :key="index">
                                                        <i class="fas fa-star"></i>
                                                    </span>
                                                    <span v-for="n in 5 - lesson.rating" :key="`empty-${n}`">
                                                    <i class="fa-regular fa-star"></i>
                                                </span>
                                                </p>
                                            </div>


                                            <!--'Remove form cart' button-->
                                            <button class="remove-from-cart-button" @click="removeFromCart(lesson._id)">
                                                Remove
                                            </button>    
                                        </div>   
                                    </div>
                                </div>
                            </div>
                    </div>

                    <!-- Order Confirmation Message-->
                    <div v-if="orderConfirmationMessage" class="order-confirmation" >
                        {{ orderConfirmationMessage}}
                    </div>

                    <!-- checkout form -->
                    <div id="checkout-form">
                        <h2>Checkout</h2>
                        <form @submit.prevent="submitForm" >
                        
                            <p>
                                <label for="name" class="form-label">Name</label>
                                <input v-model="name" type="text" class="form-control" id="name" placeholder="Fullname">
                            <p>
                            <p>
                                <label for="phone" class="form-label">Phone </label>
                                <input v-model="phone" type="number" class="form-control" id="phone" placeholder="971551234567" required>
                            <p>

                            <!-- Additional form fields can be added as needed -->
                            <button type="button" v-if="canPlaceOrder()" v-on:click="submitForm" id="btn_checkout" class="btn btn-success" data-bs-toggle="modal" data-bs-target="#orderConfirm">
                                Checkout
                            </button>
                            <button type="button" v-else id="btn_checkout" class="btn btn-success">
                                        Checkout
                            </button>

                            <!-- <button v-on:click="submitForm" >Place Order</button> -->

                        </form>
                    
                    </div>
                
 
 
 
 
 
</template>

<script>
export default {
  name: 'checkout-component',
  props: {
  }
}
</script>

<!-- Add "scoped" attribute to lim
<style scoped>
</style>
