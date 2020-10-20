<template>
  <div class="home">
    <h1>Places in Chicago</h1>
    
    <div>
      <h2>New Place</h2>
      <ul>
        <li>
          <div v-for="error in errors">{{ error }}</div>
        </li>
      </ul>
      <p>Name: <input type="text" v-model="newPlaceName"></p>
      <p>Address: <input type="text" v-model="newPlaceAddress"></p>
      <button v-on:click="createPlace()">Create</button>
    </div>

  

    <div v-for="place in places">
      <h3>Name: {{ place.name }}</h3>
      <p>Address: {{ place.address }}</p>
      <button v-on:click="showPlace(place)">More info</button>
    </div>

    <dialog id="place-details">
      <form method="dialog">
        <h2>Place Info</h2>
        <p>Name: <input type="text" v-model="currentPlace.name"></p>
        <p>Address: <input type="text" v-model="currentPlace.address"></p>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete</button>        
        <button>close</button>
      </form>
    </dialog>

  </div>
</template>


<style>
</style>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods:{ 
    indexPlaces: function () {
      axios.get("/api/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress,
      };

      axios.post("/api/places", params).then((response) => {
        console.log("Success", response.data);
        this.places.push(response.data);
      }).catch((error) => {
        this.errors = error.response.data.errors;
      });
    },
    showPlace: function (place) {
      console.log(place.name);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      var params = {
        name: place.name,
        address: place.address,
      };

      axios.patch(`/api/places/${place.id}`, params).then((response) => {
        console.log("Success", response.data);
      }).catch((error) => {
        this.errors = error.response.data.errors;
      });
    },
    destroyPlace: function (place) {
      axios.delete(`/api/places/${place.id}`).then((response) => {
        console.log("Success", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  }
};
</script>