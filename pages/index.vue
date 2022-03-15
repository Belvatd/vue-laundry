<template>
  <section class="section">
    <div class="container">
      <div class="columns">
        <div class="column is-4 is-offset-4">
          <h2 class="title has-text-centered">Welcome back!</h2>

          <Notification :message="error" v-if="error" />

          <form method="post" @submit.prevent="login">
            <div class="field">
              <label class="label">Username</label>
              <div class="control">
                <input
                  type="text"
                  class="input"
                  name="username"
                  v-model="username"
                />
              </div>
            </div>
            <div class="field">
              <label class="label">Password</label>
              <div class="control">
                <input
                  type="password"
                  class="input"
                  name="password"
                  v-model="password"
                />
              </div>
            </div>
            <div class="field">
              <label class="label">Role</label>
              <div class="control">
                <input type="text" class="input" name="role" v-model="role" />
              </div>
            </div>
            <div class="control">
              <button type="submit" class="button is-dark is-fullwidth">
                Log In
              </button>
            </div>
          </form>
          <div class="has-text-centered" style="margin-top: 20px">
            <p>
              Don't have an account?
              <nuxt-link to="/register">Register</nuxt-link>
            </p>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import Notification from "~/components/Notification";
import axios from "axios";

export default {
  layout: "clear",

  components: {
    Notification,
  },

  data() {
    return {
      username: "",
      password: "",
      role: "",
      error: null,
      logged: "",
    };
  },

  methods: {
    login() {
      let role = this.role.toLowerCase();
      let checkData = {
        username: this.username,
        password: this.password,
      };

      let url = "http://localhost:8000/api/user/login";
      axios.post(url, checkData).then((res) => {
        this.logged = res.status;
        if (this.logged === 200) {
          this.role = res.data.data.role;
          // this.$store.commit("userDetail", res.data.data);
          // this.$store.commit("get_token", res.data.data.token);
          localStorage.setItem("user", JSON.stringify(res.data.data));
          localStorage.setItem("role", res.data.data.role);
          localStorage.setItem("token", res.data.data.token);
          // localStorage.setItem("reloading", true);
          // console.log(res)

          if (this.role === "admin") {
            this.$router.push("/transaksi");
            console.log(res);
          } else if (this.role === "kasir") {
            this.$router.push("/transaksi");
          } else {
            this.$router.push("/home-owner");
          }
        }
      });
    },
  },
};
</script>
