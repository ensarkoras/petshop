<template>
  <v-container grid-list-md fluid>
    <v-layout wrap>
      <v-flex xs12 sm4 md3 v-for="pet in dogs" :key="pet.breed">
        <app-dog :dog="pet" @addToFavorites="addToFavorites"></app-dog>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import { Dogs } from '../data/dogs';
import Dog from '../components/Dog';
import {mapActions} from 'vuex'

import axios from 'axios';
axios.defaults.baseURL = 'https://dog.ceo/api';


export default {
  components: {
    appDog: Dog,
  },
  methods : {
    ...mapActions(['addToFavorites']),

  },
  data() {
    return {
      dogs: Dogs,
    };
  },
  created() {
    // We will provide an array of our requests to the first one, axios.all; it will return an array of responses and we use axios.spread to spread this array into multiple arguments. To create an array of queries we will use a .map method on our linksArray, performing axios.get for each link

    // sayfa ilk açılırken dogs.js den gelen img lar ekranda gözükmesin diye aşağıdaki kod eklendi
    this.dogs.forEach(dog => {
      dog.img = "";
    });

    const linksArray = this.dogs.map(
        dog => "/breed/" + dog.breed + "/images/random"
    );
    axios.all(linksArray.map(link => axios.get(link)))
        .then(
            axios.spread((...res) => {
              this.dogs.forEach((dog, index) => {
                dog.img = res[index].data.message;
              });
            })
        );

  }

};
</script>
