<template>
  <div
    class="card shadow"
    style="padding: 10px"
    :class="type === 'dark' ? 'bg-default' : ''"
  >
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
      <h2 style="font-size: 25px; font-family: 'Roboto'">Pending Uploads</h2>
    </div>
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
      styleClass="vgt-table"
    >
      <div slot="emptystate">No pending uploads available.</div>

      <template slot="table-row" slot-scope="props">
        <!-- Customize Photo column -->
        <span v-if="props.column.field == 'wmlink'">
          <img
            class="img-fit"
            width="600px"
            height="400px"
            :src="props.row.wmlink"
          />
        </span>

        <div v-else-if="props.column.field == 'photoName'">
          <p style="font-size: 22px; font-family: 'Roboto'">
            Photo Name: {{ props.row.photoName }}
          </p>
          <p style="font-size: 22px; font-family: 'Roboto'">
            Photo ID: {{ props.row.photoId }}
          </p>
          <p
            v-if="props.row.typeId == '1'"
            style="font-size: 22px; font-family: 'Roboto'"
          >
            License Type: Casual
          </p>
          <p
            v-else-if="props.row.typeId == '2'"
            style="font-size: 22px; font-family: 'Roboto'"
          >
            License Type: Exclusive
          </p>
          <p style="font-size: 22px; font-family: 'Roboto'">
            Description: {{ props.row.description }}
          </p>
          <p style="font-size: 22px; font-family: 'Roboto'">Categories:</p>
          <div
            style="padding-left: 15px; font-size: 25px"
            v-for="item in props.row.category"
            :key="item"
          >
            <span class="badge badge-pill badge-info">{{
              item.categoryName
            }}</span>
          </div>
          <br />
        </div>

        <div v-else>
          {{ props.formattedRow[props.column.field] }}
        </div>
      </template>
    </vue-good-table>

    <Modal v-model="detailsModal" title="Upload Details" v-bind="dataModal">
      <h3 style="text-align: center" v-bind="user">
        User: {{ user.fullName }}
      </h3>
      <h3 style="text-align: center" v-bind="user">Email: {{ user.email }}</h3>

      <img
        :src="dataModal.wmlink"
        @click="openGallery(0)"
        style="height: 400px; width: 470px; cursor: pointer"
      />
      <h4 style="text-align: center; padding: 15px">
        Click on image for full size
      </h4>

      <LightBox
        ref="lightbox"
        :showLightBox="false"
        :showThumbs="false"
        :media="[
          {
            thumb: dataModal.wmlink,
            src: dataModal.wmlink,
            srcset: dataModal.wmlink,
          },
        ]"
      >
        <inner-image-zoom :src="dataModal.wmlink" :zoomSrc="dataModal.wmlink" />
      </LightBox>

      <div class="ph-container">
        <button
          class="btn btn-success"
          style="margin-left: 55px; width: 150px"
          v-on:click="approvePhoto(dataModal.photoId)"
        >
          <span class="text-nowrap">Approve</span>
        </button>
        <button
          class="btn btn-danger"
          style="margin-left: 55px; width: 150px"
          v-on:click="openRejectModal()"
        >
          <span class="text-nowrap">Reject</span>
        </button>
      </div>
      <div class="ph-clear"></div>
    </Modal>

    <Modal v-model="chkSimilarModal" title="Similar Details" v-bind="chkSimilar" enableClose=false>
      <div style="height: 620px">
        <h2 style="text-align: center; padding: 15px">
          Detected Photo With Similarity!
        </h2>

        <h3 style="text-align: center">Photo Name: {{ chkSimilar.photoName }}</h3>

        <img
        :src="chkSimilar.wmlink"
        @click="openGallery(0)"
        style="height: 400px; width: 470px; cursor: pointer"
      />
      <h4 style="text-align: center; padding: 15px">
        Click on image for full size
      </h4>

      <LightBox
        ref="lightbox"
        :showLightBox="false"
        :showThumbs="false"
        :media="[
          {
            thumb: chkSimilar.wmlink,
            src: chkSimilar.wmlink,
            srcset: chkSimilar.wmlink,
          },
        ]"
      >
        <inner-image-zoom :src="chkSimilar.wmlink" :zoomSrc="chkSimilar.wmlink" />
      </LightBox>

      <button
          class="btn btn-danger"
          style="margin-left: 33%; width: 150px"
          v-on:click="closeModal()"
        >
          <span class="text-nowrap">Reject</span>
        </button>
      </div>
    </Modal>

    <Modal
      v-model="confirmModal"
      title="Result"
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
          <div
            class="btn btn-success"
            style="width: 150px"
            @click="reloadPage()"
          >
            OK
          </div>
        </div>
      </div>
    </Modal>

    <Modal v-model="rejectModal" title="Reject reason" style="height: 500px">
      <h3 style="text-align: center">Reason for Rejection</h3>

      <v-select
        v-model="reasons"
        :placeholder="rejectReasons[0].reportReason"
        label="reportReason"
        :options="rejectReasons"
        :searchable="false"
      ></v-select>
      <br />
      <textarea
        v-model="rejectDesc"
        style="margin-left: 60px; width: 350px; height: 150px"
        placeholder="Details about the problem..."
      ></textarea>

      <div class="ph-container">
        <div
          style="
            width: 40%;
            padding-left: 155px;
            padding-top: 35px;
            text-align: center;
          "
        >
          <button
            class="btn btn-danger"
            style="width: 150px"
            v-on:click="
              rejectPhoto(dataModal.photoId, reasons.reportReason, rejectDesc)
            "
          >
            <span class="text-nowrap">Reject</span>
          </button>
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
//import ImageLazy from "cube-vue-image-lazy";
import LightBox from "vue-it-bigger";
import("vue-it-bigger/dist/vue-it-bigger.min.css");
import InnerImageZoom from "vue-inner-image-zoom";
import vSelect from "vue-select";
import "vue-select/dist/vue-select.css";
//import modal from "@/components/Modal.vue";
export default {
  components: {
    //modal,
    VueGoodTable,
    Modal: VueModal,
    //ImageLazy,
    LightBox,
    "inner-image-zoom": InnerImageZoom,
    "v-select": vSelect,
  },
  data() {
    return {
      //model for reject reasons
      reasons: [],
      //model for reject desc
      rejectDesc: [],
      //data for main Modal
      dataModal: [],
      //model for chk similar
      chkSimilar: [],
      //user data from each rows after click
      user: [],
      //msg for confirmModal
      msg: [],
      //select-box data for rejectModal
      rejectReasons: [],
      detailsModal: false,
      confirmModal: false,
      rejectModal: false,
      chkSimilarModal: false,
      loading: false,
      loaded: false,
      //isLoading: false,

      columns: [
        {
          label: "Photo",
          field: "wmlink",
          sortable: true,
          width: "600px",
        },
        {
          label: "Photo Details",
          field: "photoName",
          type: "string",
          sortable: false,
        },
      ],
      rows: [],
    };
  },
  methods: {
    reloadPage() {
      window.location.reload();
    },
    openGallery(index) {
      this.$refs.lightbox.showImage(index);
    },
    onRowClick(params) {
      //console.log(params.row);
      this.getUserId(params.row.userId);
      //check similar photo first
      this.checkSimilarPhoto(params.row.photoId);
      this.dataModal = params.row;
      this.detailsModal = true;

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
    getReportReasons() {
      axios
        .get("https://imago.azurewebsites.net/api/v1/Report/GetAllReportReason")
        .then((response) => {
          this.rejectReasons = response.data;
          //console.log(response);
        })
        .catch((error) => {
          console.log(error);
        });
    },
    closeModal() {
      //this.detailsModal = false;
      if (this.detailsModal == true && this.chkSimilarModal == false) {
        this.detailsModal = false;
      } else if (this.detailsModal == true && this.chkSimilarModal == true) {
        this.chkSimilarModal = false;
      }
    },
    openRejectModal() {
      this.detailsModal = false;
      this.rejectModal = true;
    },
    approvePhoto(id) {
      this.detailsModal = false;
      axios
        .put("https://imago.azurewebsites.net/api/v1/User/ApprovePhoto/" + id)
        .then((response) => {
          if (response.status == 200) {
            this.confirmModal = true;
            this.msg = "Photo Approved!";
          } else {
            this.confirmModal = true;
            this.msg = "Error!";
          }
          console.log(response.status);
        })
        .catch((error) => {
          this.confirmModal = true;
          this.msg = error;
          console.log(error);
        });
    },
    rejectPhoto(dataId, rejectReason, rejectDescription) {
      this.rejectModal = false;
      axios
        .put("https://imago.azurewebsites.net/api/v1/User/DeniedPhoto", {
          id: dataId,
          reason: rejectReason,
          description: rejectDescription,
        })
        .then((response) => {
          if (response.status == 200) {
            this.confirmModal = true;
            this.msg = "Photo Rejected!";
          } else {
            this.confirmModal = true;
            this.msg = "Error!";
          }
          console.log(response.status);
        })
        .catch((error) => {
          this.confirmModal = true;
          this.msg = error;
          console.log(error);
        });
    },
    checkSimilarPhoto(id) {
      
      axios
        .get(
          "https://imago.azurewebsites.net/api/v1/Photo/GetSimilarPhoto/" + id
        )
        .then((response) => {
          if (response.status == 200) {
            this.chkSimilar = response.data;
            alert("Dectected Similar Photo!");
            this.chkSimilarModal = true;
            console.log(response.data);
          } else if (response.status == 400) {
            alert("Check Similar Passed!");
            console.log(response.data);
          }
          console.log(response);
        })
        .catch((error) => {
          alert("Check Similar Passed!");
          console.log(error);
        });
    },
  },
  mounted: function () {
    //preload reasons for v-select component or crash the web
    this.getReportReasons();
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
  padding-top: 25px;
  text-align: center;
}

.ph-clear {
  clear: both;
}
</style>
