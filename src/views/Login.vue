<template>
  <section
    class="section section-shaped section-lg my-0"
    style="background-image: url('img/theme/header-background.jpg')"
  >
  <!-- Header -->
  <div class="header bg-gradient-success py-7 py-lg-5">
      <div class="container">
        <div class="header-body text-center mb-7">
          <div class="row justify-content-center">
            <!-- <div class="col-lg-5 col-md-6">
                            <h1 class="text-white">Welcome!</h1>
                            <p class="text-lead text-white">Use these awesome forms to login or create new account in
                                your project for free.</p>
                        </div> -->
          </div>
        </div>
      </div>
    </div>
    <!-- Header -->
    <div class="container" >
      <div class="row justify-content-center">
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
                <p class="lead text-muted">Welcome to IMAGO</p>
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
    <!-- Footer -->
    <footer class="py-5">
      <div class="container">
        <div class="row align-items-center justify-content-xl-between">
          <div class="col-xl-6">
            <div class="copyright text-center text-xl-left text-muted">
              &copy; {{ year }}
              <a
                href="https://www.creative-tim.com"
                class="font-weight-bold ml-1"
                target="_blank"
                >Creative Tim</a
              >
            </div>
          </div>
          <div class="col-xl-6">
            <ul
              class="nav nav-footer justify-content-center justify-content-xl-end"
            >
              <li class="nav-item">
                <a
                  href="https://www.creative-tim.com"
                  class="nav-link"
                  target="_blank"
                  >Creative Tim</a
                >
              </li>
              <li class="nav-item">
                <a
                  href="https://www.creative-tim.com/presentation"
                  class="nav-link"
                  target="_blank"
                  >About Us</a
                >
              </li>
              <li class="nav-item">
                <a
                  href="https://www.creative-tim.com/blog"
                  class="nav-link"
                  target="_blank"
                  >Blog</a
                >
              </li>
              <li class="nav-item">
                <a
                  href="https://github.com/creativetimofficial/argon-dashboard/blob/master/LICENSE.md"
                  class="nav-link"
                  target="_blank"
                  >MIT License</a
                >
              </li>
            </ul>
          </div>
        </div>
      </div>
    </footer>
  </section>
</template>
<script>
export default {
  components: {
  },
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
.section{
  height: 100%;
 
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
