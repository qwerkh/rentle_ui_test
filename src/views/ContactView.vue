<template>
  <div>
    <h1>My Contact Page</h1>
  </div>
</template>

<script>
import axios from "axios";
const url="https://fakestoreapi.com";
export default {
  name: "ContactView",
  methods: {
    async fetchProductApi() {
      let resp = await axios({
        method: "get",
        url: url+"/products",
      });
      console.log(resp);
    },
  },
  created () {
    this.fetchProductApi();
  },
};
</script>

<style lang="scss" scoped>
</style>