<template>
  <div class="container">
    <div class="card mb-3">
      <div id="carouselExample" class="carousel slide card-img-top" style="max-width: 600px; margin: 0 auto;">
        <div class="carousel-inner">
          <div v-for="(image, index) in listing.images" :key="index" class="carousel-item"
            :class="{ 'active': index === 0 }">
            <img :src="image" class="d-block w-100" :alt="`Image ${index + 1}`">
          </div>
        </div>
        <button class="carousel-control-prev" type="button" data-bs-target="#carouselExample" data-bs-slide="prev">
          <span class="carousel-control-prev-icon" aria-hidden="true"></span>
          <span class="visually-hidden">Previous</span>
        </button>
        <button class="carousel-control-next" type="button" data-bs-target="#carouselExample" data-bs-slide="next">
          <span class="carousel-control-next-icon" aria-hidden="true"></span>
          <span class="visually-hidden">Next</span>
        </button>
      </div>
      <div class="card-body">
        <h5 class="card-title">{{ listing.title }}</h5>
        <div class="container ">
          <div class="row gx-5">
            <div class="col">
              <h5>Description</h5>
              <div class="info">

                <p class="card-text">{{ listing.description }}</p>
                <p class="card-text"> 💳 Price : {{ listing.price }}&euro; per day</p>
                <p class="card-text"> 📍 Location : {{ listing.location }}</p>
                <p class="card-text"> 🛏️ Rooms : {{ listing.rooms }}</p>
                <p>Number of days: {{ numberOfDays }}</p>
              </div>

            </div>
            <div class="col">
              <div class="p-3"></div>
              <div>
                <div class="row gx-5">
                  <div class="col">
                    <div class="p-3">Check-in date :</div>
                    <date-picker v-model="startDate" @input="onContext"></date-picker>

                    <pre class="small">{{ context }}</pre>
                  </div>

                  <div class="col">
                    <div class="p-3">Check-out date :</div>
                    <date-picker v-model="endDate" @input="onContext"></date-picker>

                    <pre class="small">{{ context }}</pre>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <p class="price-box">Total price: {{ numberOfDays * listing.price }}&euro; <button class="btn btn-primary"
              @click="reserve">Réserver</button></p>
        </div>
        <h4>Where you’ll be</h4>
        <div style="width: 100%; height: 400px;">
          <iframe :src="'https://www.google.com/maps?q=' + encodeURIComponent(listing.location) + '&output=embed'"
            width="100%" height="100%" frameborder="0" style="border:0" allowfullscreen>
          </iframe>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import axios from 'axios';
import $ from 'jquery';
import 'bootstrap';
export default {
  name: 'ListingDetails',
  data() {
    return {
      listing: {},
      startDate: null,
      endDate: null,
      context: null
    }
  },
  methods: {
    onContext(date) {
      this.context = date
    }
  },
  computed: {
    numberOfDays() {
      if (this.startDate && this.endDate) {
        const start = new Date(this.startDate);
        const end = new Date(this.endDate);
        const diffInMilliseconds = end - start;
        const diffInDays = diffInMilliseconds / (1000 * 60 * 60 * 24);
        return Math.round(diffInDays) + 1;
      }
      return 0;
    }
  },
  async created() {
    try {
      const response = await axios.get(`http://localhost:3000/houses/${this.$route.params.id}`);
      this.listing = response.data;
    } catch (error) {
      console.error(error);
    }
  },
  async reserve() {
    try {
      const response = await axios.post('http://localhost:3000/bookings', {
        username: this.$store.state.user.username, // Accéder au username à partir du store Vuex
        listingId: this.listing.id,
        location: this.listing.location,
        startDate: this.startDate,
        endDate: this.endDate
      });
      console.log(response.data);
    } catch (error) {
      console.error(error);
    }
  },
  mounted() {
    this.$nextTick(function () {
      $('#carouselExample').carousel();
    })
  }
}
</script>
<style scoped>
.container {
  background-color: #f8f9fa;
  padding: 20px;
  border-radius: 5px;
}

.card-title {
  font-size: 1.5em;
}
.price-box {
  border: 5px solid #ff6347;
  /* Change to the color you want */
  padding: 10px;
  color: white;
  /* Change to the color you want */
  float: right;
  background-color: #ff6347
}
</style>