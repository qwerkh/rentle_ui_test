<template>
  <v-container>
   <a :href="linkYoutube">Hello Youtube</a><br>
   Name: {{student.name}} <br>
   Gender:  {{student.gender}}
   <h1 v-text="address"></h1>

   <form action="" method="">

   </form>
  </v-container>
</template>
<script>
export default {
  name: 'HelloWorld',
  data:()=>{
    return {
      linkYoutube:"https://www.youtube.com/",
      address:"Battambang",
      student:{
        name:"Dara",
        gender:"Male"
      }
    }
  },
}
</script>

