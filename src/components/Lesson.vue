<template>

     
                <!-- Creating 'Lessons' page -->
                <div v-if="showLesson" class="clearfix" style="display: flex; flex-wrap: wrap; justify-content: flex-start;" >

                <!-- Sorting section -->
                <div class="sort-section" style="display: flex; flex-wrap: wrap; justify-content: space-between; width: 100%; ">
                    <!-- Sort by-->
                    <label>Sort by:</label>
                    <!--Sort by Subject-->
                    <div class="sort-options">
                        <div>
                            <input type="radio" id="sortBySubject" value="subject" v-model="sort.by">
                            <label for="sortBySubject">Subject</label>
                        </div>
                        <!--Sort by Location-->
                        <div>
                            <input type="radio" id="sortByLocation" value="location" v-model="sort.by">
                            <label for="sortByLocation">Location</label>
                        </div>
                        <!-- Sort by Price -->
                        <div>
                            <input type="radio" id="sortByPrice" value="price" v-model="sort.by">
                            <label for="sortByPrice">Price</label>
                        </div>
                        <!-- Sort by Availability-->
                        <div>
                            <input type="radio" id="sortByAvailability" value="availability" v-model="sort.by">
                            <label for="sortByAvailability">Availability</label>
                        </div>
                    </div>
                
                    <!-- Order -->
                    <label>Order:</label>
                    <div class="sort-options" >
                        <div>
                            <input type="radio" id="ascending" value="asc" v-model="sort.order">
                            <label for="ascending">Ascending</label>
                        </div>
                        <div>
                            <input type="radio" id="descending" value="desc" v-model="sort.order">
                            <label for="descending">Descending</label>
                        </div>
                    </div>
                </div>
                

                     <!--Displaying lessons-->                           
                    <div v-for="lesson in lessons" :key="lesson.id" class="lesson-card">

                        <!-- Lesson details -->
                        <div id="details">
                            <!-- Lesson information -->
                            <div id="lesson-details">

                                <!-- Subject -->
                                <div class="lesson-icons"><p>
                                    <i class="fa-solid fa-book"></i><span> Subject: {{ lesson.subject }}</span></p>
                                </div>
                                
                                <div class="lesson-icons"><p>
                                    <i class="fa-sharp fa-solid fa-location-dot"></i><span> Location: {{ lesson.location }}</span></p>
                                </div>

                                <div class="lesson-icons"><p>
                                    <i class="fas fa-dollar-sign"></i><span> Price: {{ lesson.price }}</span></p>
                                </div>

                                <div><p>
                                    <i class="fas fa-users" ></i><span>  Available Space: {{ lesson.displaySpace - cartCount (lesson._id) }}</span></p>
                                </div>

                                <!--Rating-->
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

                                <!-- 'add to cart' button-->
                                <button class="cart-button" v-on:click="addToCart(lesson)" v-bind:disabled="!canAddToCart(lesson)">
                                            Add to cart
                                </button>

                                <!-- Availability messages -->
                                <br>
                                <span v-if="computedDisplaySpace[index] === 0">
                                            All out!
                                </span>

                                <span v-else-if="computedDisplaySpace[index] < 5">>
                                        Only {{ computedDisplaySpace[index] }} left!
                                </span>
                                
                                <span v-else><br>
                                    Buy now!
                                </span>
                            </div>

                            <!-- Lesson image -->
                            <div id="image">
                                <figure>
                                    <img id="imgsize" v-bind:src="lesson.image" alt="Lesson Image">
                                </figure>                       
                            </div>
                        </div>
                    </div>     
                </div>
 
</template>

<script>
export default {
  name: 'lesson-component',
  props: {
  }
}
</script>

<!-- Add "scoped" attribute to lim
<style scoped>
</style>
