<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <div v-for="place in places" :key="place.id">
      <p>name: {{ place.name }}</p>
      <p>address: {{ place.address }}</p>
      <button v-on:click="placesShow(place)">show</button>
    </div>
    <div>
      <h5>create a place</h5>
      name:
      <input type="text" v-model="newplaceParams.name" />
      address:
      <input type="text" v-model="newplaceParams.address" />
      <button v-on:click="placesNew()">create</button>
    </div>
    <div>
      <dialog id="place-details">
        <form method="dialog">
          <h1>place info</h1>
          <p>
            name:
            <input type="text" v-model="currentplace.name" />
          </p>
          <p>
            address:
            <input type="text" v-model="currentplace.address" />
          </p>
          <button>close</button>
          <button v-on:click="placesUpdate(currentplace)">update</button>
          <button v-on:click="placesDestroy(currentplace)">delete</button>
        </form>
      </dialog>
    </div>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "places",
      places: [],
      newplaceParams: {},
      currentplace: {},
    };
  },
  created: function () {
    this.placesIndex();
  },
  methods: {
    placesIndex: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        console.log("all places", response.data);
        this.places = response.data;
      });
    },
    placesNew: function () {
      axios
        .post("http://localhost:3000/places", this.newplaceParams)
        .then((response) => {
          console.log("new place", response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          console.log("update error", error.response);
        });
    },
    placesShow: function (place) {
      axios.get(`http://localhost:3000/places/${place.id}`, this.newplaceParams).then((response) => {
        console.log("show place", response.data);
        this.currentplace = response.data;
        document.querySelector("#place-details").showModal();
      });
    },
    placesUpdate: function (place) {
      axios
        .patch(`http://localhost:3000/places/${place.id}`, place)
        .then((response) => {
          console.log("place updated", response.data);
          this.currentplace = {};
        })
        .catch((error) => {
          console.log("update error", error.response);
        });
    },
    placesDestroy: function () {
      axios.delete(`http://localhost:3000/places/${this.currentplace.id}`).then((response) => {
        console.log("destroyed", response.data);
      });
    },
  },
};
</script>
