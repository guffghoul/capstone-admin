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

        <p
          v-else-if="props.column.field == 'similarPhoto'"
          style="text-align: center; margin-top: 150%"
        >
          <span
            v-if="props.row.similarPhoto == null"
            style="
              border-radius: 70px;
              border: 12px solid #32cd32;
              background-color: #32cd32;
              color: white;
              font-size: 18px;
              font-family: 'Roboto';
            "
          >
            Undetected
          </span>
          <span
            v-else-if="props.row.similarPhoto != null"
            style="
              border-radius: 70px;
              border: 12px solid #dc143c;
              background-color: #dc143c;
              color: white;
              font-size: 18px;
              font-family: 'Roboto';
            "
          >
            Detected
          </span>
        </p>

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
            style="font-size: 25px; font-family: 'Roboto'"
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

    <Modal
      v-model="detailsModal"
      title="Upload Details"
      v-bind="dataModal"
      :modal-style="{ 'max-width': '65%' }"
    >
      <div style="height: 580px; display: flex">
        <div style="width: 45%; margin: auto">
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
            <inner-image-zoom
              :src="dataModal.wmlink"
              :zoomSrc="dataModal.wmlink"
            />
          </LightBox>
        </div>

        <div
          style="
            width: 45%;
            text-align: left;
            padding-left: 50px;
            padding-top: 25px;
          "
        >
          <p style="font-size: 22px; font-family: 'Roboto'" v-bind="user">
            User: {{ user.fullName }}
          </p>
          <p style="font-size: 22px; font-family: 'Roboto'" v-bind="user">
            Email: {{ user.email }}
          </p>
          <p style="font-size: 22px; font-family: 'Roboto'">
            Photo Name: {{ dataModal.photoName }}
          </p>
          <p
            v-if="dataModal.typeId == '1'"
            style="font-size: 22px; font-family: 'Roboto'"
          >
            License Type: Casual
          </p>
          <p
            v-else-if="dataModal.typeId == '2'"
            style="font-size: 22px; font-family: 'Roboto'"
          >
            License Type: Exclusive
          </p>
          <p style="font-size: 22px; font-family: 'Roboto'">
            Price: {{ dataModal.price }} $
          </p>
          <p style="font-size: 22px; font-family: 'Roboto'">
            Description: {{ dataModal.description }}
          </p>
          <p style="font-size: 22px; font-family: 'Roboto'">Categories:</p>
          <div
            style="
              font-size: 25px;
              font-family: 'Roboto';
              margin-left: 40%;
              margin-top: -50px;
              margin-bottom: 15%;
            "
            v-for="item in dataModal.category"
            :key="item"
          >
            <span class="badge badge-pill badge-info">{{
              item.categoryName
            }}</span>
          </div>
          <div style="margin-top: -5%">
            <button
              class="btn btn-success"
              style="width: 150px"
              v-on:click="approvePhoto(dataModal.photoId)"
            >
              <span class="text-nowrap">Approve</span>
            </button>
            <button
              class="btn btn-danger"
              style="margin-left: 25px; width: 150px"
              v-on:click="openRejectModal()"
            >
              <span class="text-nowrap">Reject</span>
            </button>
          </div>
        </div>
      </div>
    </Modal>

    <Modal
      v-model="chkSimilarModal"
      title="Similar Details"
      v-bind="chkSimilar"
      enableClose="false"
      :modal-style="{ 'max-width': '85%' }"
    >
      <h2
        style="
          text-align: center;
          padding: 10px;
          margin-left: auto;
          margin-right: auto;
          display: block;
        "
      >
        Detected Photo With Similarity!
        <i class="fa fa-exclamation-circle" aria-hidden="true"></i>
      </h2>
      <div style="height: 520px; display: flex">
        <div style="width: 45%; margin: auto">
          <h2 style="text-align: center">Current Photo</h2>
          <h3 style="text-align: center" v-bind="dataModal">
            Photo Name: {{ dataModal.photoName }}
          </h3>

          <img
            :src="dataModal.wmlink"
            @click="openGallery(0)"
            style="
              height: 400px;
              width: 470px;
              cursor: pointer;
              margin-left: auto;
              margin-right: auto;
              display: block;
            "
          />
          <h4 style="text-align: center; padding: 10px">
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
            <inner-image-zoom
              :src="dataModal.wmlink"
              :zoomSrc="dataModal.wmlink"
            />
          </LightBox>
        </div>

        <div style="width: 45%; margin: auto">
          <h2 style="text-align: center">Similarity Target</h2>
          <h3 style="text-align: center">
            Photo Name: {{ chkSimilar.photoName }}
          </h3>

          <img
            :src="chkSimilar.wmlink"
            @click="openGallery(0)"
            style="
              height: 400px;
              width: 470px;
              cursor: pointer;
              margin-left: auto;
              margin-right: auto;
              display: block;
            "
          />
          <h4 style="text-align: center; padding: 10px">
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
            <inner-image-zoom
              :src="chkSimilar.wmlink"
              :zoomSrc="chkSimilar.wmlink"
            />
          </LightBox>
        </div>
      </div>
      <div style="display: flex">
        <button
          class="btn btn-success"
          style="
            width: 150px;
            margin-left: auto;
            margin-right: auto;
            display: block;
          "
          v-on:click="resolveModal(dataModal)"
        >
          <span class="text-nowrap">Resolve</span>
        </button>
        <button
          class="btn btn-danger"
          style="
            width: 150px;
            margin-left: auto;
            margin-right: auto;
            display: block;
          "
          v-on:click="
            rejectPhoto(
              dataModal.photoId,
              'Similarity Rejection',
              'Your photo was detected to have many similarity with the old photo before' 
            )
          "
        >
          <span class="text-nowrap">Reject</span>
        </button>
      </div>
    </Modal>

    <Modal
      v-model="confirmModal"
      title="Result"
      style="height: 500px; margin: auto"
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
export default {
  components: {
    // modal,
    VueGoodTable,
    Modal: VueModal,
    // Unauthorized,
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
          label: "Similarity Check",
          field: "similarPhoto",
          sortable: true,
          sortFn: this.sortFn,
          width: "150px",
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
  // computed: {
  //   isLoggedIn() {
  //     return this.$store.getters.isLoggedIn;
  //   },
  // },
  methods: {
    sortFn(x, y) {
      // x - row1 value for column
      // y - row2 value for column
      // col - column being sorted
      // rowX - row object for row1
      // rowY - row object for row2
      return x == null ? -1 : y == null ? 1 : 0;
    },
    reloadPage() {
      window.location.reload();
    },
    openGallery(index) {
      this.$refs.lightbox.showImage(index);
    },
    resolveModal(data) {
      this.chkSimilarModal = false;
      this.dataModal = data;
      this.detailsModal = true;
    },
    onRowClick(params) {
      //console.log(params.row);
      this.getUserId(params.row.userId);
      //check similar photo first
      if (params.row.similarPhoto != null) {
        this.dataModal = params.row;
        this.checkSimilarPhoto(params.row.similarPhoto);
      } else {
        this.dataModal = params.row;
        this.detailsModal = true;
      }

      // params.row - row object
      // params.pageIndex - index of this row on the current page.
      // params.selected - if selection is enabled this argument
      // indicates selected or not
      // params.event - click event
    },
    getUserId(id) {
      axios
        .get(
          "https://capstoneprojectapi20210418160622.azurewebsites.net/api/v1/User/GetById/" +
            id
        )
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
        .get(
          "https://capstoneprojectapi20210418160622.azurewebsites.net/api/v1/Report/GetAllReportReason"
        )
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
      let loader = this.$loading.show({
        loader: "dots",
        height: 50,
        width: 50,
      });
      axios({
        url: "http://localhost:2000/transactions",
        data: {
          transactionId: 'none',
          prevOwner: this.dataModal.photoName,
          ownerID: this.dataModal.userId,
          photoId: this.dataModal.photoId,
          photoHash: this.dataModal.phash,
          isTransaction: false,
          amount: this.dataModal.price,
          createDate: new Date().toISOString(),
        },
        method: "POST",
        headers: { "Content-Type": "application/json" },
      })
        .then((response) => {
          if (response.status == 200) {
            this.detailsModal = false;
            axios
              .put(
                "https://capstoneprojectapi20210418160622.azurewebsites.net/api/v1/User/ApprovePhoto/" +
                  id
              )
              .then((resp) => {
                if (resp.status == 200) {
                  loader.hide();
                  this.confirmModal = true;
                  this.msg = "Photo Approved!";
                } else {
                  loader.hide();
                  this.confirmModal = true;
                  this.msg = "Error in approving photo!";
                }
                console.log(resp.status);
              })
              .catch((error) => {
                loader.hide();
                this.confirmModal = true;
                this.msg = error;
              });
          }
        })
        .catch((error) => {
          loader.hide();
          console.log(error)
          this.confirmModal = true;
          this.msg = "Save to BC Error!";
        });
    },
    rejectPhoto(dataId, rejectReason, rejectDescription) {
      let loader = this.$loading.show({
        loader: "dots",
        height: 50,
        width: 50,
      });
      this.rejectModal = false;
      // if (rejectReason == null) {
      //   rejectReason = this.rejectReasons[0].reportReason;
      // }
      // if (rejectDescription.length == 0) {
      //   rejectDescription.push("None");
      // }
      // console.log(dataId);
      // console.log(rejectReason);
      // console.log(rejectDescription);
      axios
        .put(
          "https://capstoneprojectapi20210418160622.azurewebsites.net/api/v1/User/DeniedPhoto",
          {
            id: dataId,
            reason: rejectReason,
            description: rejectDescription,
          }
        )
        .then((response) => {
          if (response.status == 200) {
            loader.hide();
            this.confirmModal = true;
            this.msg = "Photo Rejected!";
          } else {
            loader.hide();
            this.confirmModal = true;
            this.msg = "Error!";
          }
          // console.log(response.status);
        })
        .catch((error) => {
          loader.hide();
          this.confirmModal = true;
          this.msg = error;
          console.log(error);
        });
    },
    checkSimilarPhoto(photo) {
      this.chkSimilar = photo;
      this.chkSimilarModal = true;
    },
  },
  mounted: function () {
    //preload reasons for v-select component or crash the web
    this.getReportReasons();
    let isLoggedIn = this.$store.getters.isLoggedIn;
    if (isLoggedIn == false) {
      this.$router.push("/unauthorized");
    } else {
      let loader = this.$loading.show({
        loader: "dots",
        height: 50,
        width: 50,
      });
      axios
        .get(
          "https://capstoneprojectapi20210418160622.azurewebsites.net/api/v1/Photo/getToApprove"
        )
        .then((response) => {
          this.rows = response.data;
          loader.hide();
        })
        .catch((error) => {
          console.log(error);
        });
    }
  },
};
</script>
<style>
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
