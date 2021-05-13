<template>
  <div class="card shadow" :class="type === 'dark' ? 'bg-default' : ''">
    <div
      class="card-header border-0"
      :class="type === 'dark' ? 'bg-transparent' : ''"
    >
      <div class="row align-items-center">
        <div class="col">
          <h3 class="mb-0" :class="type === 'dark' ? 'text-white' : ''">
            {{ title }}
          </h3>
        </div>
      </div>
    </div>

    <div style="text-align: center">
      <h2 style="font-size: 25px; font-family: 'Roboto'">Create Categories</h2>
    </div>
    <br />

    <form
      role="form"
      @submit.prevent="createCategory"
      style="
        width: 40%;
        height: 450px;
        margin: auto;
        text-align: center;
        padding-top: 10%;
      "
    >
      <base-input
        v-model="cateName"
        alternative
        class="mb-3"
        placeholder="New Category Name"
      >
      </base-input>
      <span style="color: red" v-if="msg.cateName">{{ msg.cateName }}</span>
      <div class="row justify-content-center">
        <button class="btn btn-success" style="width: 150px">
          <span class="text-nowrap">Create</span>
        </button>
      </div>
    </form>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      cateName: "",
      listCate: [],
      msg: [],
    };
  },
  watch: {
    cateName(value) {
      this.cateName = value;
      this.validateCateName(value);
    },
  },
  methods: {
    validateCateName(value) {
      this.listCate.forEach((element) => {
        if (value.toLowerCase() == element.categoryName.toLowerCase()) {
          console.log("checked " + element.categoryName.toLowerCase());
          this.msg["cateName"] = "This category name already existed!";
        } else {
          this.msg["cateName"] = "";
        }
      });
    },

    createCategory() {
      let name = this.cateName;
      axios
        .post(
          "https://capstoneprojectapi20210418160622.azurewebsites.net/api/v1/Category",
          {
            categoryName: name,
            description: "",
          }
        )
        .then((response) => {
          console.log(response);
        })
        .catch((error) => {
          alert("Category already exist!");
          console.log(error);
        });
    },
  },
  mounted: function () {
    let isLoggedIn = this.$store.getters.isLoggedIn;
    if (isLoggedIn == false) {
      this.$router.push("/unauthorized");
    } else {
      axios
        .get(
          "https://capstoneprojectapi20210418160622.azurewebsites.net/api/v1/Category"
        )
        .then((response) => {
          this.listCate = response.data;
        });
    }
  },
};
</script>
<style>
</style>
