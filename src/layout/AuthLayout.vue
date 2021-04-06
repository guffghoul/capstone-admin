<template>
     <div class="content" style="background-image: linear-gradient(to right, #ffafbd, #ffb3b4, #ffb7ac, #ffbda5, #ffc3a0);">
      <div class="container row justify-content-center">
        <div class="col-lg-5">
          <card
            type="secondary"
            shadow
            header-classes="bg-white pb-5"
            body-classes="px-lg-5 py-lg-5"
            class="border-0"
            style="background-color:#FFFFFF"
          >
            <template>
              <div class="text-center text-muted mb-4">
                <p class="lead text-muted">Welcome back admin</p>
              </div>
              <form role="form" @submit.prevent="login">
                <base-input
                  v-model="username"
                  alternative
                  class="mb-3"
                  placeholder="Username"
                  addon-left-icon="ni ni-email-83"
                >
                </base-input>
                <base-input
                  v-model="password"
                  alternative
                  type="password"
                  placeholder="Password"
                  addon-left-icon="ni ni-lock-circle-open"
                >
                </base-input>
                <div class="row justify-content-center">
                  <button class="login-button" type="submit">Sign in</button>
                </div>
              </form>
            </template>
          </card>
          <div class="row mt-3">
            <div class="col-6">
              <router-link to="/requestchangepassword" title="Register">
                <a href="#" style="color:#0886DE">
                  <small>Forgot password?</small>
                </a>
              </router-link>
            </div>
            <div class="col-6 text-right">
              <router-link to="/register" title="Register">
                 <a href="#" style="color:#0886DE">
                  <small>Create new account</small>
                </a>
              </router-link>
            </div>
          </div>
        </div>
      </div>
    </div> 
 
   
 
</template>
<script>
export default {
  data() {
    return {
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
              console.log(err),
              alert("The username and / or password is incorrect")
            )
          );
        this.$store.dispatch("auth");
      }
    },
  },
};
</script>
<style scoped>
.content{
   
    height: 100%;
    background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
.container{
    margin-inline:  auto;
}    

.login-button {
  background-color: #525f7f;
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  margin: 4px 2px;
  cursor: pointer;
  font-size: 12px;
  border-radius: 12px;
}
</style>
