<template>
  <div>
    <h1>My Product Page</h1>

    <v-col cols="12" md="12" sm="12">
      <v-btn @click="handleAdd()" rounded="xs" size="x-large" color="primary">
        <v-icon>mdi-plus</v-icon> Add Product
      </v-btn>
    </v-col>

    <v-data-table-server
      v-model:items-per-page="itemsPerPage"
      :headers="headers"
      :items="serverItems"
      :items-length="totalItems"
      :loading="loading"
      :search="search"
      item-value="name"
      @update:options="loadItem"
    >
      <template v-slot:item.url="{ item }">
        <v-card class="my-2" elevation="2" rounded>
          <v-img :src="item.url" height="64" cover></v-img>
        </v-card>
      </template>

      <template v-slot:item.postDate="{ item }">
        {{ formatDate(item.postDate) }}
      </template>

      <template v-slot:item.actions="{ item }">
        <v-btn
          icon="mdi-pencil"
          class="me-2"
          color="blue"
          size="small"
          @click="handleEdit(item)"
        />

        <v-btn
          icon=" mdi-delete"
          size="small"
          color="red"
          @click="deleteItem(item)"
        />
      </template>
    </v-data-table-server>

    <!-- For add data -->
    <v-dialog v-model="dialog" max-width="600">
      <v-form ref="form">
        <v-card prepend-icon="mdi-plus" :title="action + ' Product'">
          <v-card-text>
            <v-row>
              <v-col cols="12" md="6" sm="6">
                <v-text-field
                  v-model="productDoc.name"
                  :counter="10"
                  :rules="nameRules"
                  label="Product Name"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" md="6" sm="6">
                <v-text-field
                  v-model="productDoc.desc"
                  label="Description"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12" md="6" sm="6">
                <v-text-field
                  v-model="productDoc.price"
                  :rules="numberRules"
                  label="Product Price"
                  type="number"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" md="6" sm="6">
                <v-autocomplete
                  v-model="productDoc.category"
                  :items="cateogryOpt"
                  label="Category"
                  item-title="name"
                  item-value="id"
                  clearable
                >
                </v-autocomplete>
              </v-col>
              <v-col cols="12" md="6" sm="6">
                <v-switch
                  v-model="productDoc.status"
                  label="Available"
                  hide-details
                  color="red-darken-3"
                  inset
                ></v-switch>
              </v-col>
              <v-col cols="12" md="6" sm="6">
                <v-menu :close-on-content-click="false" v-model="menuPostDate">
                  <template v-slot:activator="{ props }">
                    <v-text-field
                      v-bind="props"
                      v-model="postDateFormated"
                      label="Date"
                      prepend-inner-icon="mdi-calendar-range"
                      readonly
                      required
                    ></v-text-field>
                  </template>

                  <v-date-picker
                    v-model="productDoc.postDate"
                    color="primary"
                  ></v-date-picker>
                </v-menu>
              </v-col>

              <v-col cols="12" md="6" sm="6">
                <v-radio-group v-model="productDoc.type" inline>
                  <v-radio label="Real Estate" value="RealEstate"></v-radio>
                  <v-radio
                    label="Non Real Estate"
                    value="NonRealEstate"
                  ></v-radio>
                </v-radio-group>
              </v-col>

              <v-col cols="12" md="6" sm="6">
                <v-checkbox
                  v-model="productDoc.page"
                  label="Home"
                  value="Home"
                ></v-checkbox>
                <v-checkbox
                  v-model="productDoc.page"
                  label="News"
                  value="News"
                ></v-checkbox>
                <v-checkbox
                  v-model="productDoc.page"
                  label="Category"
                  value="Category"
                ></v-checkbox>
              </v-col>
            </v-row>
          </v-card-text>

          <v-divider></v-divider>

          <v-card-actions>
            <v-spacer></v-spacer>

            <v-btn text="Close" variant="plain" @click="dialog = false"></v-btn>

            <v-btn color="primary" @click="submitData()"> Save </v-btn>
          </v-card-actions>
        </v-card>
      </v-form>
    </v-dialog>
  </div>
</template>
<script>
// Import Package
import moment from "moment";
import { isProxy, toRaw } from "vue";
import myMix from "@/mixins/my-mix.vue";
//Fake Data
let productList = [
  {
    _id: "aa",
    name: "Room A",
    desc: "BTB",
    price: 20000,
    category: "House",
    status: true,
    postDate: "2024-11-07",
    type: "RealEstate",
    page: "Home",
    url: "https://images.pexels.com/photos/610293/pexels-photo-610293.jpeg",
  },
  {
    _id: "bb",
    name: "Room B",
    desc: "PP",
    price: 30000,
    category: "Condo",
    status: true,
    postDate: "2024-11-07",
    type: "RealEstate",
    page: "Home",
    url: "https://images.pexels.com/photos/3680219/pexels-photo-3680219.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1",
  },
  {
    _id: "cc",
    name: "Room B",
    desc: "PP",
    price: 30000,
    category: "Condo",
    status: true,
    postDate: "2024-11-07",
    type: "RealEstate",
    page: "Home",
    url: "https://images.pexels.com/photos/3680219/pexels-photo-3680219.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1",
  },
  {
    _id: "dd",
    name: "Room B",
    desc: "PP",
    price: 30000,
    category: "Condo",
    status: true,
    postDate: "2024-11-07",
    type: "RealEstate",
    page: "Home",
    url: "https://images.pexels.com/photos/3680219/pexels-photo-3680219.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1",
  },
  {
    _id: "ee",
    name: "Room B",
    desc: "PP",
    price: 30000,
    category: "Condo",
    status: true,
    postDate: "2024-11-07",
    type: "RealEstate",
    page: "Home",
    url: "https://images.pexels.com/photos/3680219/pexels-photo-3680219.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1",
  },
  {
    _id: "ff",
    name: "Room B",
    desc: "PP",
    price: 30000,
    category: "Condo",
    status: true,
    postDate: "2024-11-07",
    type: "RealEstate",
    page: "Home",
    url: "https://images.pexels.com/photos/3680219/pexels-photo-3680219.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1",
  },
  {
    _id: "gg",
    name: "Room B",
    desc: "PP",
    price: 30000,
    category: "Condo",
    status: true,
    postDate: "2024-11-07",
    type: "RealEstate",
    page: "Home",
    url: "https://images.pexels.com/photos/3680219/pexels-photo-3680219.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1",
  },
  {
    _id: "hh",
    name: "Room B",
    desc: "PP",
    price: 30000,
    category: "Condo",
    status: true,
    postDate: "2024-11-07",
    type: "RealEstate",
    page: "Home",
    url: "https://images.pexels.com/photos/3680219/pexels-photo-3680219.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1",
  },
  {
    _id: "ii",
    name: "Room B",
    desc: "PP",
    price: 30000,
    category: "Condo",
    status: true,
    postDate: "2024-11-07",
    type: "RealEstate",
    page: "Home",
    url: "https://images.pexels.com/photos/3680219/pexels-photo-3680219.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1",
  },
  {
    _id: "jj",
    name: "Room B",
    desc: "PP",
    price: 30000,
    category: "Condo",
    status: true,
    postDate: "2024-11-07",
    type: "RealEstate",
    page: "Home",
    url: "https://images.pexels.com/photos/3680219/pexels-photo-3680219.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1",
  },
  {
    _id: "kk",
    name: "Room B",
    desc: "PP",
    price: 30000,
    category: "Condo",
    status: true,
    postDate: "2024-11-07",
    type: "RealEstate",
    page: "Home",
    url: "https://images.pexels.com/photos/3680219/pexels-photo-3680219.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1",
  },
  {
    _id: "ll",
    name: "Room B",
    desc: "PP",
    price: 30000,
    category: "Condo",
    status: true,
    postDate: "2024-11-07",
    type: "RealEstate",
    page: "Home",
    url: "https://images.pexels.com/photos/3680219/pexels-photo-3680219.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1",
  },
];
// setTimeout(()=>{
// },500)
const FakeAPI = {
  async fetch({ page, itemsPerPage, sortBy }) {
    return new Promise((resolve) => {
      setTimeout(() => {
        const start = (page - 1) * itemsPerPage;
        const end = start + itemsPerPage;
        const items = productList.slice();

        if (sortBy.length) {
          const sortKey = sortBy[0].key;
          const sortOrder = sortBy[0].order;
          items.sort((a, b) => {
            const aValue = a[sortKey];
            const bValue = b[sortKey];
            return sortOrder === "desc" ? bValue - aValue : aValue - bValue;
          });
        }

        const paginated = items.slice(start, end);

        resolve({ items: paginated, total: items.length });
      }, 500);
    });
  },
  async add(doc) {
    return new Promise((resolve) => {
      setTimeout(() => {
        productList.unshift(toRaw(doc));

        resolve({ status: 201 });
      }, 500);
    });
  },
  async edit(id, doc) {
    return new Promise((resolve) => {
      setTimeout(() => {
        let ind = productList.findIndex((d) => d._id == id);
        productList[ind] = doc;
        resolve({ status: 201 });
      }, 500);
    });
  },
  
  async remove(doc) {
    return new Promise((resolve) => {
      setTimeout(() => {
        let ind = productList.findIndex((d) => d._id == doc._id);
        productList.splice(ind,1);
        resolve({ status: 201 });
      }, 500);
    });
  },
};

export default {
  name: "ProductView",
  mixins:[myMix],
  data() {
    return {
      // Option data
      cateogryOpt: [
        { name: "House", id: "001" },
        { name: "Flat", id: "002" },
        { name: "Condo", id: "003" },
        { name: "Apartment", id: "004" },
      ],

      // Variable Data
      dialog: false,

      menuPostDate: false,
      postDateFormated: moment().format("DD/MM/YYYY"),
      productDoc: {
        _id: "",
        name: "",
        desc: "",
        price: "",
        category: "",
        status: true,
        postDate: moment().toDate(),
        type: "RealEstate",
        page: "Home",
        url: "",
      },

      // Rule  Data
      nameRules: [
        (v) => !!v || "Name is required",
        (v) => (v && v.length <= 10) || "Name must be 10 characters or less",
      ],
      numberRules: [(v) => v > 1 || "Number should greater than 1"],

      //Table Display
      itemsPerPage: 5,
      headers: [
        {
          title: "Product Name",
          align: "start",
          sortable: false,
          key: "name",
        },
        { title: "Photo", key: "url" },
        { title: "Price", key: "price", align: "end" },
        { title: "Description", key: "desc", align: "end" },
        { title: "Category", key: "category", align: "end" },
        { title: "Post Date", key: "postDate", align: "end" },
        { title: "Type", key: "type", align: "end" },
        { title: "Page", key: "page", align: "end" },
        { title: "Status", key: "status", align: "end" },
        { title: "Actions", key: "actions", sortable: false },
      ],
      search: "",
      serverItems: [],
      loading: true,
      totalItems: 0,
      action: "",
    };
  },
  methods: {
    async submitData() {
      let vm = this;
      const { valid } = await this.$refs.form.validate();

      if (valid) {
        // alert(vm.productDoc.name);
        let tmpDoc = Object.assign({}, vm.productDoc);
        if (
          tmpDoc._id === "" ||
          tmpDoc._id === null ||
          tmpDoc._id === undefined
        ) {
          FakeAPI.add(tmpDoc).then(({ status }) => {
            if (status == 201) {
              vm.loadItem({
                page: 1,
                itemsPerPage: vm.itemsPerPage,
                sortBy: [],
              });
            }
          });
        } else {
          FakeAPI.edit(tmpDoc._id, tmpDoc).then(({ status }) => {
            if (status == 201) {
              vm.loadItem({
                page: 1,
                itemsPerPage: vm.itemsPerPage,
                sortBy: [],
              });
            }
          });
        }
        vm.dialog = false;
      }
    },
    handleAdd() {
      let vm = this;
      vm.dialog = true;
      vm.action = "Add";
    },
    loadItem({ page, itemsPerPage, sortBy }) {
      this.loading = true;
      FakeAPI.fetch({ page, itemsPerPage, sortBy }).then(({ items, total }) => {
        this.serverItems = items;
        this.totalItems = total;

        this.loading = false;
      });
    },
  
    handleEdit(doc) {
      let vm = this;
      vm.action = "Update";
      console.log(toRaw(doc));
      vm.productDoc = Object.assign({}, toRaw(doc));
      vm.dialog = true;
    },
    deleteItem(doc) {
      let vm=this;
      swal(
        {
          title: "Are you sure?",
          text: "You will not be able to recover this imaginary file!",
          type: "warning",
          showCancelButton: true,
          confirmButtonColor: "#DD6B55",
          confirmButtonText: "Yes, delete it!",
          closeOnConfirm: false,
          //closeOnCancel: false
        },
        function () {
          FakeAPI.remove(doc).then(({ status }) => {
            if (status == 201) {
              swal("Deleted!", "Your imaginary file has been deleted!", "success");
              vm.loadItem({
                page: 1,
                itemsPerPage: vm.itemsPerPage,
                sortBy: [],
              });
            }
          });
         
        }
      );
    },
  },
  watch: {
    "productDoc.postDate"(newValue) {
      let vm = this;
      vm.postDateFormated = moment(newValue).format("DD/MM/YYYY");
      vm.menuPostDate = false;
    },
    dialog(val) {
      let vm = this;
      if (val == true && vm.action == "Add") {
        vm.$nextTick(() => {
          vm.$refs.form.reset();
          vm.productDoc.postDate = moment().toDate();
        });
      }
    },
  },
};
</script>

<style lang="scss" scoped></style>
