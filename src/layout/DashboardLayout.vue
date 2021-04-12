<template>
  <div class="wrapper" :class="{ 'nav-open': $sidebar.showSidebar }">
    <side-bar
      :background-color="sidebarBackground"
      short-title="Imago"
      title="Imago"
    >
      <template slot="links">
        <sidebar-item
          :link="{
            name: 'Dashboard',
            icon: 'ni ni-tv-2 text-primary',
            //path: '/dashboard'
            path: '/dashboard',
          }"
        />

        <sidebar-item
          :link="{
            name: 'Reports',
            icon: 'ni ni-bullet-list-67 text-red',
            path: '/tables',
          }"
        />
        <!-- <sidebar-item :link="{name: 'Icons', icon: 'ni ni-planet text-blue', path: '/icons'}"/>
        <sidebar-item :link="{name: 'Maps', icon: 'ni ni-pin-3 text-orange', path: '/maps'}"/>
        <sidebar-item :link="{name: 'User Profile', icon: 'ni ni-single-02 text-yellow', path: '/profile'}"/>
        <sidebar-item :link="{name: 'Tables', icon: 'ni ni-bullet-list-67 text-red', path: '/tables'}"/> -->
         <sidebar-item v-if="!isLoggedIn" :link="{name: 'Login', icon: 'ni ni-key-25 text-info', path: '/login'}"/>
          <a class="nav-link nav-link-icon" @click="logout" v-if="isLoggedIn">
            <i class="ni ni-user-run"></i>
            <span class="nav-link-inner--text">Sign out</span>
          </a>
        
      </template>
    </side-bar>
    <div class="main-content" :data="sidebarBackground">
      <dashboard-navbar></dashboard-navbar>

      <div @click="toggleSidebar">
        <fade-transition :duration="200" origin="center top" mode="out-in">
          your content here
          <router-view></router-view>
        </fade-transition>
        <content-footer v-if="!$route.meta.hideFooter"></content-footer>
      </div>
    </div>
  </div>
</template>
<script>
import DashboardNavbar from "./DashboardNavbar.vue";
import ContentFooter from "./ContentFooter.vue";
import { FadeTransition } from "vue2-transitions";
import SidebarItem from "../components/SidebarPlugin/SidebarItem.vue";

export default {
  components: {
    DashboardNavbar,
    ContentFooter,
    FadeTransition,
    SidebarItem,
  },
  data() {
    return {
      sidebarBackground: "vue", //vue|blue|orange|green|red|primary
    };
  },
  computed: {
    isLoggedIn() {
      return this.$store.getters.isLoggedIn;
    },
  },
  methods: {
    toggleSidebar() {
      if (this.$sidebar.showSidebar) {
        this.$sidebar.displaySidebar(false);
      }
    },
    logout() {
      this.$store.dispatch("logout").then(() => {
        this.$router.push("/");
      });
    },
  },
};
</script>
<style lang="scss"></style>
