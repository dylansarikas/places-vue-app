<script>
import axios from "axios";

export default {
  data: function () {
    return {
      message: "Welcome to the Places frontend!",
      places: [],
      newPlace: {},
      currentPlace: {},
      editPlace: {},
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      axios
        .post("http://localhost:3000/places", this.newPlace)
        .then((response) => {
          console.log(response.data);
          this.places.unshift(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
      this.newPlace = {};
    },
    showPlace: function (place) {
      this.editPlace = place;
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      axios
        .patch(`http://localhost:3000/places/${place.id}`, place)
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    destroyPlace: function (place) {
      if (confirm("Are you sure about that?")) {
        axios.delete(`http://localhost:3000/places/${place.id}`).then((response) => {
          console.log(response.data);
          var index = this.places.indexOf(place);
          this.places.splice(index, 1);
        });
      }
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <div v-for="place in places" v-bind:key="place.id">
      <h2>{{ place.name }}</h2>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h1>Place Info</h1>
        <p>
          Name:
          <input type="text" v-model="editPlace.name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="editPlace.address" />
        </p>
        <button v-on:click="updatePlace(editPlace)">Update</button>
        <button v-on:click="destroyPlace(editPlace)">Delete</button>
        <button>Close</button>
      </form>
    </dialog>
    <h2>New Place</h2>
    <p>{{ newPlace }}</p>
    <p>
      Name:
      <input type="text" v-model="newPlace.name" />
    </p>
    <p>
      Address:
      <input type="text" v-model="newPlace.address" />
    </p>
    <div>
      <button v-on:click="createPlace()">New Place</button>
    </div>
    <div>
      <button v-on:click="indexPlaces()">Index Places</button>
    </div>
  </div>
</template>

<style></style>
