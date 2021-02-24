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

    <div><h3 style="padding-left: 3em">Pending Uploads</h3></div>
    <br />

    <mdb-container>
      <mdb-datatable-2 v-model="data" striped bordered arrows :display="2" hover>
        
      </mdb-datatable-2>
    </mdb-container>

    <!-- <div
      class="card-footer d-flex justify-content-end"
      :class="type === 'dark' ? 'bg-transparent' : ''"
    >
      <base-pagination total="30"></base-pagination>
    </div> -->
  </div>
</template>

<script>
import { mdbDatatable2, mdbContainer } from "mdbvue";
export default {
  name: "projects-table",
  components: {
    mdbDatatable2,
    mdbContainer,
  },
  data() {
    return {
      data: {
        rows: [],
        columns: [],
      },
    };
  },
  methods: {
    filterData(dataArr, keys) {
      let data = dataArr.map((entry) => {
        let filteredEntry = {};
        keys.forEach((key) => {
          if (key in entry) {
            filteredEntry[key] = entry[key];
          }
        });
        return filteredEntry;
      });
      return data;
    },
  },
  mounted() {
    fetch("https://capstonerestapi.azurewebsites.net/api/v1/Photo/getToApprove")
      .then((res) => res.json())
      .then((json) => {
        let keys = ["photoId", "photoName", "userId"];
        let entries = this.filterData(json, keys);
        //
        console.log(entries);
        entries.forEach((item) => {
          fetch(
            "https://capstonerestapi.azurewebsites.net/api/v1/User/GetById/"+item.userId
          )
            .then((res) => res.json())
            .then((json) => {
              console.log(json);
              let keys = ["username", "fullName"];
              let names = this.filterData(json, keys);
              console.log(names);
              if (item.userId == names.userId) {
                item.userId = names.fullname;
              }
              //console.log(item);
            });
        });
        //
        //columns
        const columns = keys.map((key) => {
          return {
            label: key.toUpperCase(),
            field: key,
            sort: true,
          };
        });
        //rows

        this.data = {
          columns,
          rows: entries,
        };
      })
      .catch((err) => console.log(err));
  },
};
</script>
<style>
</style>
