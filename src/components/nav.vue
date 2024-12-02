<template>
  <nav>
    <router-link
      v-for="(navDoc, ind) in navbarList"
      :key="ind"
      :to="navDoc.path"
      >{{ navDoc.title }}​</router-link
    >

    <!-- <router-link to="/about">About</router-link> | 
        <router-link to="/contact">Contact</router-link> | 
        <router-link to="/product">Product</router-link> -->

    <!-- <a href="/contact">Contact</a> -->
  </nav>
</template>

<script>
export default {
  name: "NavView",
  data() {
    return {
      navbarList: [
        { title: "Home | ", path: "/" },
        { title: "About | ", path: "/about" },
        { title: "Contact | ", path: "/contact" },
        { title: "Product | ", path: "/product" },
        { title: "Category", path: "/category" },
      ],
    };
  },
};
</script>

<style lang="scss" scoped>
</style>