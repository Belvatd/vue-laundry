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
    login(event) {
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
          let token = res.data.data.token;
          let user = res.data.data;
          let role = res.data.data.role;
          localStorage.setItem("user", JSON.stringify(user));
          localStorage.setItem("token", token);
          localStorage.setItem("role", role);
            console.log(res)

          if (role === "admin") {
            this.$router.push("/transaksi");
            console.log(res)
          }
          else if(role === "kasir"){
            this.$router.push("/transaksi");
          }
          else{
            this.$router.push("/list outlet");
          }
        }
      });
    },
    // login(event) {
    //   let role = this.role.toLowerCase();
    //   let checkData = {
    //     username: this.username,
    //     password: this.password,
    //   };

    //   if (role === "siswa") {
    //     let url_siswa = "http://localhost:5000/siswa/login";
    //     axios
    //       .post(url_siswa, checkData)
    //       .then((response) => {
    //         this.logged = response.data.logged;
    //         if (this.logged === true) {
    //           this.role = response.data.role;
    //           let user = response.data.data;
    //           let token = response.data.token;
    //           let role = response.data.role;
    //           localStorage.setItem("user", JSON.stringify(user));
    //           localStorage.setItem("token", token);
    //           localStorage.setItem("role", role);
    //           window.location = "/siswa/home";
    //           console.log(response);
    //         } else {
    //           console.log("gagal masuk");
    //         }
    //       })
    //       .catch((error) => console.log(error));
    //   } else {
    //     let url_admin_petugas = "http://localhost:5000/v1/petugas/login";
    //     axios
    //       .post(url_admin_petugas, checkData)
    //       .then((response) => {
    //         this.logged = response.data.data.logged;
    //         if (this.logged === true) {
    //           this.role = response.data.data.role;
    //           let user = response.data.data;
    //           let token = response.data.data.token;
    //           let fixRole = response.data.data.role.toLowerCase();
    //           localStorage.setItem("user", JSON.stringify(user));
    //           localStorage.setItem("token", token);
    //           localStorage.setItem("role", fixRole);

    //           if (fixRole === "admin") {
    //             window.location = "/transaksi";
    //             console.log(response);
    //           } else {
    //             window.location = "/siswa";
    //           }
    //         } else {
    //           console.log("gagal masuk");
    //           console.log(response.logged);
    //         }
    //       })
    //       .catch((error) => console.log(error));
    //     console.log("gagal masuk");
    //   }
    // },
  },
};
</script>
