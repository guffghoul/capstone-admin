<template>
  <div class="row justify-content-center">
    <div class="col-lg-5 col-md-7">
      <div class="card bg-secondary shadow border-0">
        <!-- <div class="card-header bg-transparent pb-5">
                        <div class="text-muted text-center mt-2 mb-3"><small>Sign in with</small></div>
                        <div class="btn-wrapper text-center">
                            <a href="#" class="btn btn-neutral btn-icon">
                                <span class="btn-inner--icon"><img src="img/icons/common/github.svg"></span>
                                <span class="btn-inner--text">Github</span>
                            </a>
                            <a href="#" class="btn btn-neutral btn-icon">
                                <span class="btn-inner--icon"><img src="img/icons/common/google.svg"></span>
                                <span class="btn-inner--text">Google</span>
                            </a>
                        </div>
                    </div> -->
        <div class="card-body px-lg-5 py-lg-5">
          <!-- <div class="text-center text-muted mb-4">
                            <small>Or sign in with credentials</small>
                        </div> -->
          <div class="text-muted text-center mt-1 mb-3"><h2>Login Form</h2></div>
          <form role="form" @submit.prevent="login">
            <base-input
              class="input-group-alternative mb-3"
              placeholder="Username"
              addon-left-icon="ni ni-email-83"
              v-model="username"
            >
            </base-input>

            <base-input
              class="input-group-alternative"
              placeholder="Password"
              type="password"
              addon-left-icon="ni ni-lock-circle-open"
              v-model="password"
            >
            </base-input>

            <!-- <base-checkbox class="custom-control-alternative">
                                <span class="text-muted">Remember me</span>
                            </base-checkbox> -->
            <div class="text-center">
              <button type="submit" class="btn btn-primary">Sign in</button>
            </div>
          </form>
        </div>
      </div>
      <!-- <div class="row mt-3">
                    <div class="col-6">
                        <a href="#" class="text-light"><small>Forgot password?</small></a>
                    </div>
                    <div class="col-6 text-right">
                        <router-link to="/register" class="text-light"><small>Create new account</small></router-link>
                    </div>
                </div> -->
    </div>
  </div>
</template>
<script>
export default {
  //name: "login",
  data() {
    return {
      // model: {
      //   email: '',
      //   password: ''
      // }
      username: "",
      password: "",
    };
  },
  methods: {
    login() {
      let username = this.username;
      let password = this.password;
      if (username === "" || password === "") {
        alert("Username and password must not empty");
      } else {
        this.$store
          .dispatch("login", { username, password })
          .then(() => this.$router.push("/dashboard"))
          .catch(
            (err) => (
              console.log("err:"),
              console.log(err),
              alert("The username and / or password is incorrect")
            )
          );
      }
    },
  },
};
</script>
<style>
</style>
