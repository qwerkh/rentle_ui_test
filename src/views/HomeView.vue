<template>
  <div class="home">
    <h1>My Home Page</h1>
    <ul>
      <li
        v-for="(mycolor, ind) in colorList"
        :key="ind"
        :style="{color:mycolor}"
      >
        {{ mycolor }}
      </li>
    </ul>
    <!-- :style="'color:'+mycolor" -->

    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <h1 v-show="studentName">Student Name : {{ studentName }}</h1>
    <center>
      <table border="1">
        <thead>
          <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Phone Number</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="stDoc in studentList"
            :key="stDoc.id"
           
          >
            <td>{{ stDoc.id }}</td>
            <td>{{ stDoc.name }}</td>
            <td>{{ stDoc.phoneNumber }}</td>
            <td><v-btn class="primary"  @click="clickonrow(stDoc)">Show</v-btn></td>

          </tr>
        </tbody>
      </table>
      <about-view v-if="studentName" :fname="studentName" @saveToChild="callFromChild"></about-view>

      <product-view></product-view>
    </center>
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue'
import ProductView from '@/views/ProductView.vue'
import AboutView from './AboutView.vue';
export default {
  name: "HomeView",
  components: {
    ProductView,
    AboutView
  },
  data() {
    return {
      colorList: ["Red", "Blue", "Pink", "Yellow"],
      studentList: [
        { id: "01", name: "Dara", phoneNumber: "012336333" },
        { id: "02", name: "Makarka", phoneNumber: "012336333" },
        { id: "03", name: "Kanha", phoneNumber: "012336333" },
      ],
      studentName: "",
      
    };
  },
  methods: {
    clickonrow(doc) {
      let vm = this;
      vm.studentName = vm.studentName ? "" : doc.name;
    },
    callFromChild(doc){
        let vm=this;
        vm.studentName=doc;
    }
  },
};
</script>
