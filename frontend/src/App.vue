<template>
  <div id="app">
    <b-container>
      <b-row class="text-left">
          <b-col><h2>Flamegrapher 🔥</h2></b-col>
      </b-row>
      <b-row align-h="center" align-v="center">
        <flower-spinner :animation-duration="2500" :size="100" color="#b95e42" v-show="loading"/>
      </b-row>
      <router-view :items="items" v-show="!loading"></router-view>
    </b-container>
  </div>
</template>
<script>
import axios from "axios";

export default {
  name: "app",
  data () {
    return {
      items: [],
      loading: true
    };
  },
  created () {
    this.$nextTick(function () {
      axios
        .get("/api/list/")
        .then(response => {
          this.items = response.data;
          this.loading = false;
        })
        .catch(e => {
          console.log(e);
        });
    });
  }
};
</script>


<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 40px;
}

h1,
h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

.btn-link,
.btn-outline-secondary,
a,
h2 {
  color: #b95e42;
}
</style>
