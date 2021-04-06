<template>
    <div class="content divElement">
        <h2 style="color:#069">Unauthorized Access</h2>
        <section class="content"
            style="background-image: url('img/theme/error.png')"
        >
        <router-link>
            <base-button>
            Click here to login
        </base-button>
        </router-link>
        
    
        </section>
    </div>
</template>
<script>
import BaseButton from '../components/BaseButton.vue';
export default {

  components: { BaseButton },
  data() {
 
  },
  methods: {
   
  },
};
</script>
<style scoped>
.divElement{
    text-align: center;
    margin-top: 5%;
}
.content {
    tex-align: center;
    height: 600px;
    width: 800px;
    display: block;
    margin-left: auto;
    margin-right: auto;

}
</style>
