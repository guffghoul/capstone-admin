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

    <div><h3 style="text-align: center">Pending Uploads</h3></div>
    <br />

    <vue-good-table
      :pagination-options="{
        enabled: true,
        perPage: 5,
      }"
      :rows="rows"
      :columns="columns"
      :fixed-header="true"
      @on-row-click="onRowClick"
    >
      <div slot="emptystate">No pending uploads available.</div>

      <template slot="table-row" slot-scope="props">
        <!-- Customize Photo column -->
        <span v-if="props.column.field == 'wmlink'">
          <img width="200px" height="150px" :src="props.row.wmlink" />
        </span>

        <!-- Customize Type column -->
        <span v-else-if="props.column.field == 'typeId'">
          <p v-if="props.row.typeId == '1'">Non-Exclusive</p>
          <p v-else-if="props.row.typeId == '2'">Exclusive</p>
        </span>

        <span v-else>
          {{ props.formattedRow[props.column.field] }}
        </span>
      </template>
    </vue-good-table>

    <Modal v-model="showModal" title="Upload Details" v-bind="dataModal">
      <h3 style="text-align: center" v-bind="user">
        User: {{ user.fullName }}
      </h3>
      <h3 style="text-align: center" v-bind="user">Email: {{ user.email }}</h3>
      <ImageLazy
        class="photo"
        :src="dataModal.wmlink"
        :srcset="dataModal.wmlink"
        baseClass="image-lazy"
        deferredClass="image-lazy-deferred"
        loadingClass="image-lazy-loading"
        loadedClass="image-lazy-loaded"
        :delay="0"
        @loading="loading = true"
        @load="loaded = true"
      />
      <div class="ph-container">
        <div class="ph-float">
          <a
            v-on:click="approvePhoto(dataModal.photoId)"
            class="ph-button ph-btn-green"
            >Approve</a
          >
        </div>
        <div class="ph-float">
          <a v-on:click="closeModal()" class="ph-button ph-btn-red">Reject</a>
        </div>
      </div>
      <div class="ph-clear"></div>
    </Modal>

    <Modal
      v-model="confirmModal"
      title="Success"
      style="height: 500px"
      v-bind="msg"
    >
      <h3 style="text-align: center">{{ msg }}</h3>
      <div class="ph-container">
        <div
          style="
            width: 40%;
            padding-left: 155px;
            padding-top: 35px;
            text-align: center;
          "
        >
          <a href="/maps" class="ph-button ph-btn-green">OK</a>
        </div>
      </div>
    </Modal>
  </div>
</template>

<script>
import "vue-good-table/dist/vue-good-table.css";
import { VueGoodTable } from "vue-good-table";
import axios from "axios";
import VueModal from "@kouts/vue-modal";
import "@kouts/vue-modal/dist/vue-modal.css";
import ImageLazy from "cube-vue-image-lazy";
export default {
  components: {
    VueGoodTable,
    Modal: VueModal,
    ImageLazy,
  },
  data() {
    return {
      dataModal: [],
      user: [],
      msg: [],
      showModal: false,
      confirmModal: false,
      loading: false,
      loaded: false,
      //isLoading: false,
      columns: [
        {
          label: "Photo",
          field: "wmlink",
          sortable: false,
          width: "200px",
        },
        {
          label: "Photo ID",
          field: "photoId",
          type: "number",
          width: "150px",
          sortable: true,
        },
        {
          label: "Photo Name",
          field: "photoName",
          type: "string",
          sortable: false,
        },
        {
          label: "Type",
          field: "typeId",
          type: "string",
          width: "150px",
          sortable: true,
        },
        {
          label: "UserID",
          field: "userId",
          sortable: false,
          hidden: true,
        },
      ],
      rows: [
        // {
        //   userId: "Abu",
        //   photoName: "Alladin (1992)",
        //   photoId: "Joe Grant",
        //   wmLink:
        //     "https://i.kym-cdn.com/photos/images/original/001/925/277/f22.png",
        // },
        // {
        //   userId: "Magoc",
        //   photoName: "Koelle (1992)",
        //   photoId: "Mascintos",
        //   wmLink:
        //     "https://m.media-amazon.com/images/I/816CDZPqTyL._AC_SL1471_.jpg",
        // },
        // {
        //   userId: "Temps",
        //   photoName: "Erocv (1992)",
        //   photoId: "Joe MOMO",
        //   wmLink:
        //     "https://i.kym-cdn.com/photos/images/original/001/925/277/f22.png",
        // },
      ],
    };
  },
  methods: {
    onRowClick(params) {
      //console.log(params.row);
      this.getUserId(params.row.userId);
      this.showModal = true;
      this.dataModal = params.row;
      console.log(this.dataModal);
      // params.row - row object
      // params.pageIndex - index of this row on the current page.
      // params.selected - if selection is enabled this argument
      // indicates selected or not
      // params.event - click event
    },
    getUserId(id) {
      axios
        .get("https://imago.azurewebsites.net/api/v1/User/GetById/" + id)
        .then((response) => {
          this.user = response.data;
          console.log(response);
        })
        .catch((error) => {
          console.log(error);
        });
    },
    closeModal() {
      this.showModal = false;
    },
    approvePhoto(id) {
      this.showModal = false;
      axios
        .put("https://imago.azurewebsites.net/api/v1/User/ApprovePhoto/" + id)
        .then((response) => {
          if (response.status == 200) {
            this.confirmModal = true;
            this.msg = "Approved!";
          } else {
            this.msg = "Error!";
          }
          console.log(response.status);
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  mounted: function () {
    axios
      .get("https://imago.azurewebsites.net/api/v1/Photo/getToApprove")
      .then((response) => {
        this.rows = response.data;
        console.log(response);
      })
      .catch((error) => {
        console.log(error);
      });
  },
};
</script>
<style>
image-lazy {
  opacity: 0;
}
.image-lazy-loading {
  filter: blur(5px);
  transform: translateY(1rem);
  width: 29.6em;
  height: 25em;
  padding-right: 5px;
}
.image-lazy-loaded {
  transition: opacity 2s ease, transform 1s ease, filter 1s ease;
  opacity: 1;
  transform: none;
  filter: none;
  width: 29.6em;
  height: 25em;
  padding-right: 5px;
}
.ph-button {
  width: 9em;
  border-style: solid;
  border-width: 0px 0px 3px;
  box-shadow: 0 -1px 0 rgba(255, 255, 255, 0.1) inset;
  color: #ffffff;
  border-radius: 6px;
  cursor: pointer;
  display: inline-block;
  font-style: normal;
  overflow: hidden;
  text-align: center;
  text-decoration: none;
  text-overflow: ellipsis;
  transition: all 200ms ease-in-out 0s;
  white-space: nowrap;
  font-family: "Gotham Rounded A", "Gotham Rounded B", Helvetica, Arial,
    sans-serif;
  font-weight: 700;
  padding: 19px 39px 18px;
  font-size: 18px;
}
.ph-btn-green {
  border-color: #3ac162;
  background-color: #5fcf80;
}
.ph-btn-green:hover {
  color: blanchedalmond;
}
.ph-btn-green:focus,
.ph-btn-green:active {
  background-color: #4bc970;
  border-color: #3ac162;
}
.ph-btn-red {
  background-color: #ed5a5a !important;
  border-color: #ea4343 !important;
}
.ph-btn-red:hover {
  color: blanchedalmond;
}
.ph-btn-red:focus,
.ph-btn-red:active {
  background: none repeat scroll 0 0 #eb4848 !important;
  border-color: #e83131 !important;
}
.ph-container {
  margin: 0 auto;
  display: inline;
}

.ph-float {
  float: left;
  width: 40%;
  padding-left: 65px;
  padding-top: 35px;
  text-align: center;
}

.ph-clear {
  clear: both;
}
</style>
