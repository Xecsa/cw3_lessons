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
        <Lesson v-model:sort-order="sortOrder" v-model:sort-by="sortBy" :lessons="lessons" v-on:add-to-cart="addToCart" :can-add-to-cart="canAddToCart" :cart-count="cartCount"/>    
      </div>

    <div v-else>
      <Checkout :submitForm="submitForm" :orderConfirmationMessage="orderConfirmationMessage" :lessons="lessons" :cartLessons="cartLessons" v-on:remove-item-from-cart="removeFromCart"/>
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
  },
   data(){ return {
                    sitename: "Lessons Hub",
                    showLesson: true,
                    isCartEmpty: true,
                    showCheckoutPage: false,
                    orderConfirmationMessage: '',
                    name: '',
                    phone: '',
                    search: '',
                    cartLessons:[],
                    lessons: [], // Assuming you have included lesson.js
                    numberOfSpaces: 1,
                    sortBy: "subject",
                    sortOrder: "asc",  
                    cart: [],
                    showPlaceOrder: false,
                    displayCart: [],
                   
            }},
            // Mounted hook

            mounted(){
                this.fetchAllLessons();
            },

            //the methods of Vue instance
            methods: {   
                // Fetch all lessons with GET
                fetchAllLessons() {
                    fetch('http://localhost:3000/lessons')
                    .then(response => response.json())
                    .then(data => {
                    console.log(data)
                    // Assuming 'lessons' is the property in your data where you store lessons
                    this.lessons = data;
                      })
                    .catch(error => console.error('Error fetching lessons:', error));
                },

                // Save a new order with POST
                saveNewOrder(orderData) {
                     fetch('http://localhost:3000/orders', {
                         method: 'POST',
                         headers: {
                            'Content-Type': 'application/json',
                        },
                         body: JSON.stringify(orderData),
                     })
                         .then(response => response.json())
                         .then(data => {
                          console.log(data);
                         // Gives the confirmation message  
                         this.orderConfirmationMessage = 'Order submitted. Thank you!';
 
                    //Displays an alert
                     alert('Order submitted. Thank you!');


                // Optionally, you can fetch all lessons again after adding a new order
                     this.fetchAllLessons();
                     for (let i = 0; i < this.cartLessons.length; i++) {
                         this.updateLessonSpace(this.cartLessons[i], this.numberOfSpaces)
                     }
                })
                     .catch(error => console.error('Error adding order:', error));
            },

                    // Update available lesson space with PUT
                    updateLessonSpace(lessonId, numberOfSpaces) {
                        fetch(`http://localhost:3000/lessons/${lessonId}`, {
                                  method: 'PUT',
                            headers: {
                                 'Content-Type': 'application/json',
                             },
                            body: JSON.stringify({ space: numberOfSpaces }),
                        })
                         .then(response => response.json())
                         .then(data => {
                         // Process the response as needed
                         console.log('Lesson space updated:', data);
                         // Optionally, you can fetch all lessons again after updating lesson space
                         this.fetchAllLessons();
                         })
                        .catch(error => console.error('Error updating lesson space:', error));
                    },

                    // Add lesson to cart
                    addToCart: function (lesson) {
                        if (this.canAddToCart(lesson)) {
                            this.cartLessons.push(lesson._id);
                            this.cart.push(lesson._id) 
                            this.isCartEmpty = false; //sets to false when a lesson is added
                        }
                     },
                     // Toggle checkout page
                    showCheckout() {
                            this.showCheckoutPage = !this.showCheckoutPage;
                    },
                    // Submit form
                    submitForm(order) {
                      console.log("submitted");
                        let orderData = {lessons: this.cartLessons, numberOfSpaces: this.numberOfSpaces, ...order}
                        this.saveNewOrder(orderData);

                    },

                    // Count items in the cart
                    cartCount(id) {
                        let count = 0;
                        for (let i = 0; i < this.cart.length; i++) {
                            if (this.cart[i] === id) {
                                count++;
                            }
                        }
                        return count;
                    },

                    // Check if lesson can be added to cart
                    canAddToCart(lesson) {
                        const availableSpace = lesson.displaySpace - (this.availableSpaceMap[lesson._id] || 0);
                        return availableSpace > 0;
                    },

                    //Search for lessons
                    customSearch(){
                        fetch(`http://localhost:3000/search?keyword=${this.search}`)
                            .then(res => {
                                 return res.json()
                                 })
                            .then(data => {
                                 this.lessons = data
                            })
                            .catch(err => {
                                 this.lessons = []
                                 console.log(`unable to get lessons: ${err}`)
                            })
                    },

                    // Remove item from cart
                    removeFromCart(lessonId){
                        //Remove the lesson from the cartLessons array
                        const index = this.cartLessons.indexOf(lessonId);
                        if (index !== -1){
                            this.cartLessons.splice(index, 1);
                        }
                    
                        // Remove lesson from cart array
                        const cartIndex = this.cart.indexOf(lessonId);
                        if (cartIndex !== -1){
                            this.cart.splice(cartIndex, 1);
                        }
                        
                        //Checks if the cart is empty
                        this.isCartEmpty = this.cart.length === 0;

                    },

                    // Return lesson to lesson list
                    returnToLessonList(lessonId){
                        //Finds the lesson with the given lessonId in the lessons array
                        const lessonToAddBack = this.lessons.find(lesson => lesson._id === lessonId);

                        // Checks if the lesson is not already in the lessons array before adding it
                        if(!this.lessons.some(lesson => lesson._id === lessonId )){
                            //Adds the lesson back to the lessons array
                            this.lessons.push(lessonToAddBack);

                        }

                    },
                },
                

                
                // Computed properties
                computed: {
                    // Computed property to calculate available space for each lesson
                    availableSpaceMap(){
                        const spaceMap = {};
                        this.cart.forEach((lessonId) => {
                            if (!spaceMap[lessonId]) {
                                spaceMap[lessonId] = 0;
                            }
                            spaceMap[lessonId] += 1;
                        });
                        return spaceMap;
                    },

                    // Compute cart item count
                    cartItemCount: function () {
                        return this.cart.length;
                    },

                    // search and sorting products
                    sortedLessons() {

                        //Search
                        if (this.search !== ""){
                            //Used the custom search function for search results
                            return this.customSearch();
                        }else{
                          alert('here')
                        let lessonsArr = this.lessons.slice(0);

                        //sorting price
                        if (this.sortBy === 'price'){
                            return this.sortOrder === 'asc' ?
                                lessonsArr.sort((a, b) => a.price - b.price) :
                                lessonsArr.sort((a, b) => b.price - a.price);
                        }
                    
                        // sorting spaces
                        if (this.sortBy === 'availability') {
                            return this.sortOrder === 'asc' ?
                                lessonsArr.sort((a, b) => (a.displaySpace - this.cartCount(a._id)) - (b.displaySpace - this.cartCount(b._id))) :
                                lessonsArr.sort((a, b) => (b.displaySpace - this.cartCount(b._id)) - (a.displaySpace - this.cartCount(a._id)));
                        }

                        // sorting subject
                        if (this.sortBy === 'subject') {
                            return lessonsArr.sort((a, b) => {
                                const subjectA = a.subject.toLowerCase();
                                const subjectB = b.subject.toLowerCase();
                                if (this.sortOrder === 'asc') {
                                     return a.subject.localeCompare(subjectB);
                                } else {
                                    return b.subject.localeCompare(subjectA);
                                }
                            });
                         
                         }

                        // sorting location
                        if (this.sortBy === 'location') {
                            return lessonsArr.sort((a, b) => {
                                if (this.sortOrder === 'asc') {
                                    return a.location.localeCompare(b.location);
                                } else {
                                     return b.location.localeCompare(a.location);
                                 }
                            });
                         }

                        // Default case, return the original array
                        return lessonsArr;
                    }
                },
                },
}
</script>

<style>
/* style.css */

body {
    font-family: 'Arial', sans-serif;
    margin: 0;
    padding: 0px;
    background-color: #f0f0f0;
    color: #333;
    overflow-x: hidden;
}

/* Nav bar style */
nav {
    background-color: #3498db; 
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 15px; 
    box-sizing: border-box;
    margin-bottom: 30px;
}


nav h1 {
    color: #fff; 
    margin: 0;
    margin-right: 650px; 
}


nav img {
    height: 50px;
    width: 50px;
}

nav .search-bar {
    margin-bottom: 10px;
}

nav .checkout-button {
    background-color: #27ae60; 
    color: #fff; 
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

nav .checkout-button:hover {
    background-color: #219d54; 
}


#app {
    max-width: 100%;
    margin: 0 auto;
    background-color: #fff; 
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    border-radius: 8px;
    
}

header {
    text-align: center;
    margin-bottom: 20px;
}

button {
    background-color: #e74c3c; 
    color: #fff;
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

button:hover {
    background-color: #c0392b; 
}


/* Show lesson style */
main {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    margin-top: 30px;
}

.lesson-card {
    width: 400px;
    height: 400px;
    margin: 10px;
    padding: 20px;
    background-color: #ffcc99; 
    color: #333;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    border-radius: 8px;
    box-sizing: border-box;
    transition: box-shadow 0.3s ease;
    
}

/* Clearfix for lesson cards to handle wrapping */
.clearfix::after {
    content: "";
    display: table;
    clear: both;
}

.lesson-card:hover {
    transform: translateY-5px();
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.3);
    transition: transform 0.3s ease, box-shadow 0.3 ease 0.2;
}

.lesson-card img {
    width: 130px;
    height: 130px;
    padding-top: 10px;
    display: inline-block;
    border-radius: 8px;
    margin-bottom: 20px;
}

.lesson-card p {
    margin-bottom: 10px;
}

.lesson-card button {
    background-color: #ff5722; 
    color: #333; 
    border: none;
    padding: 10px 20px;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease, color 0.3s ease;
}

.lesson-card button:hover {
    background-color: #e64a19; 
}

.lesson-icons {
    display: flex;
    margin-bottom: 10px;
}

.lesson-icons i {
    margin-right: 5px;
    color: #ff9800; 
}

#details {
    display: inline-flex;
}


#lesson-details {
    width: 200px;

}

nav .search-bar {
    margin-top: 10px;
    position: relative;
}

nav .search-bar input[type="text"] {
    padding: 8px;
    border: none;
    border-radius: 5px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    width: 200px; 
}

nav .search-bar button {
    background-color: #3498db; 
    color: #fff;
    border: none;
    border-radius: 5px;
    padding: 8px 12px;
    margin-left: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

nav .search-bar button:hover {
    background-color: #2980b9; 
}


/*Footer Style */
footer {
    background-color: #333; 
    color: #fff; 
    padding: 20px; 
    text-align: center; 
}

h1 {
    margin: 0; 
    font-size: 1.5em; 
}

/* Style for the sorting section */
.sort-section {
    margin-top: 20px;
    flex-wrap: wrap;
    background-color: #f5f5f5;
    padding: 15px;
    border-radius: 8px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.sort-section label {
    margin-bottom: 0px;
    color: #007bff;
    font-weight: bold;
    display: block;
}

.sort-options {
    display: flex;
    align-items: center;
}

.sort-options div {
    margin-right: 15px; 

}


.sort-options input[type="radio"] {
    display: none;
}

.sort-options label {
    cursor: pointer;
    background-color: #007bff;
    color: #fff;
    padding: 10px 15px;
    border-radius: 5px;
    transition: background-color 0.3s ease, color 0.3s ease;
}

.sort-options label:hover {
    background-color: #0056b3;
}

.sort-options input[type="radio"]:checked + label {
    background-color: #0056b3;
}

.sort-options input,
.sort-options select {
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 4px;
}

/* Checkout Page Styles */
#Checkout-cart {
    width: 100%;
    margin: 20px auto;
    padding: 20px;
    background-color: #f8f9fa;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    border-radius: 8px;
}

#Checkout-cart h2 {
    color: #007bff; /* Blue color */
    text-align: center;
}

.cart-items {
    width: 100%;
    box-sizing: border-box;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    border: 1px solid #dee2e6; /* Light gray border */
    margin: 10px 0;
    padding: 15px;
    background-color: #fff; /* White background */
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    border-radius: 8px;
}


.cart-item-image {
    width: 130px;
    height: 130px;
    display: inline-block;
    border-radius: 8px;
}

#Checkout-cart .cart-item {
    display: flex;
    margin-bottom: 15px;
}

#Checkout-cart .lesson-card {
    flex: 1;
    margin-right: 0;
    border: 1px solid #dee2e6;
    border-radius: 8px;
    overflow: hidden;
}

#Checkout-cart .lesson-card .lesson-details {
    padding: 15px;
}

#Checkout-cart .lesson-details {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    width: 200px;
    height: 300px;
}


#Checkout-cart .lesson-details p {
    margin: 5px 0;
    font-size: 16px;
}

/* Style the remove-from-cart-button within lesson-details */
#Checkout-cart .lesson-details .remove-from-cart-button {
    background-color: #dc3545; /* Red color */
    color: #fff;
    border: none;
    padding: 8px 12px;
    cursor: pointer;
    border-radius: 5px;
    transition: background-color 0.3s;
}

#Checkout-cart .lesson-details .remove-from-cart-button:hover {
    background-color: #c82333; /* Darker red on hover */
}

/* Style the cart-item-image within lesson-details */
#Checkout-cart .lesson-details .cart-item-image {
    width: 80px;
    height: 80px;
    border-radius: 8px;
    margin-right: 10px;
}

/* Checkout form style */
#checkout-form {
    max-width: 400px;
    margin: 20px auto;
    padding: 20px;
    background-color: #fff;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    text-align: center; 

    /* Border styles */
    border: 2px solid #3498db;
    border-radius: 8px; 
}

#checkout-form h2 {
    margin-bottom: 20px;
}

#checkout-form form {
    text-align: left;
}

#checkout-form p {
    margin-bottom: 15px;
}

#checkout-form label {
    display: inline-block;
    width: 120px; 
}

#checkout-form input {
    display: inline-block;
    width: calc(100% - 120px);
    flex: 1; 
    padding: 8px;
    box-sizing: border-box;
    margin-bottom: 10px;
}

#checkout-form button {
    background-color: #4caf50;
    color: #fff;
    border: none;
    padding: 10px 15px;
    cursor: pointer;
    font-size: 16px;
}

#checkout-form button:hover {
    background-color: #45a049;
}

.order-confirmation {
    margin-top: 20px;
    padding: 10px;
    background-color: #dff0d8; 
    border: 1px solid #d6e9c6; 
    color: #3c763d;
    border-radius: 4px;
}

</style>
