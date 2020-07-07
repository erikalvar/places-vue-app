<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <div v-for="place in places">
      <h3>{{ place.name }}</h3>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>

    <dialog id="place-info">
      <form method="dialog">
        <h1>Place Info</h1>
        <p>Name: <input type="text" v-model="currentPlace.name"></p>
        <p>Address: <input type="text" v-model="currentPlace.address"></p>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button v-on:click=destroyPlace(currentPlace)>Delete</button>
        <button>Close</button>
      </form>
    </dialog>

    <div id="addNew">
      <h1>Add New Place</h1>
      <p>Name: <input type="text" v-model="newPlaceName"></p>
      <p>Address: <input type="text" v-model="newPlaceAddress"></p>
      <button v-on:click="createPlace()">Create</button>
    </div>
  </div>
</template>

<style>
</style>

<script>
import axios from "axios";
export default {
  data: function() {
    return {
      message: "Places Index",
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      errors: [],
      currentPlace: {}
    };
  },
  created: function() {
    axios.get("/api/places").then(response => {
      console.log("All Places", response.data);
      this.places = response.data;
    });
  },
  methods: {
    createPlace: function() {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress
      };
      axios
        .post("/api/places", params)
        .then(response => {
          console.log("Success".response.data);
          this.places.push(response.data);
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function(place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-info").showModal();
    },
    updatePlace: function(place) {
      var params = {
        name: place.name,
        address: place.address
      };
      axios.patch(`/api/places/${place.id}`, params).then(response => {
        console.log("Successfully Updated", response.data);
      });
    },
    destroyPlace: function(place) {
      axios.delete(`/api/places/${place.id}`).then(response => {
        console.log("Successfully Deleted", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    }
  }
};
</script>