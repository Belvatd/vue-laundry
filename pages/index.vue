<template>
  <v-app id="inspire">
    <v-content>
      <v-container fluid fill-height>
        <v-layout align-center justify-center>
          <v-flex xs12 sm8 md4>
            <v-card class="elevation-12">
              <v-toolbar dark color="primary">
                <v-toolbar-title>Welcome to Clean n Fresh</v-toolbar-title>
              </v-toolbar>
              <v-card-text>
                <v-form>
                  <v-text-field
                    name="login"
                    label="Login"
                    type="text"
                    v-model="username"
                  ></v-text-field>
                  <v-text-field
                    id="password"
                    name="password"
                    label="Password"
                    type="password"
                    v-model="password"
                  ></v-text-field>
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="primary" @click="login">Login</v-btn>
              </v-card-actions>
            </v-card>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
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
          localStorage.setItem("id_user", res.data.data.id_user);
          localStorage.setItem("role", res.data.data.role);
          localStorage.setItem("token", res.data.data.token);

          if (this.role === "admin") {
            this.$router.push("/dashboard-admin");
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
