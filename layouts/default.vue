<template>
  <v-app light>
    <div v-if="isadmin">
      <v-navigation-drawer
      v-if="isadmin"
      class="primary"
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title class="sideText" v-text="item.title" />
            <!-- <p>{{ loggedInUser.username }}</p> -->
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar :clipped-left="clipped" fixed app>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-toolbar-title v-text="title" />
      <v-spacer />
    </v-app-bar>
    </div>
    
    <!-- <div v-else>
      
    </div> -->
    

    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer :absolute="!fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: "DefaultLayout",
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: "mdi-apps",
          title: "Transaksi",
          to: "/transaksi",
        },
        {
          icon: "mdi-accounts-bubble",
          title: "Histori Transaksi",
          to: "/histori",
        },
        {
          icon: "mdi-accounts-bubble",
          title: "Daftar Member",
          to: "/list-member",
        },
        {
          icon: "mdi-accounts-bubble",
          title: "Daftar Outlet",
          to: "/list-outlet",
        },
        {
          icon: "mdi-accounts-bubble",
          title: "Daftar User Admin",
          to: "/list-admin",
        },
        {
          icon: "mdi-accounts-bubble",
          title: "Daftar User Owner",
          to: "/list-owner",
        },
        {
          icon: "mdi-accounts-bubble",
          title: "Daftar User Kasir",
          to: "/list-kasir",
        },
      ],
      miniVariant: false,
      right: false,
      rightDrawer: false,
      title: "Clean Laundry",
      token: "",
      role: "",
      isadmin: false,
      iskasir: false,
      isowner:false
    };
  },

  mounted() {
    // this.getToken();
    this.isAdmin();
    this.isKasir();
    this.isOwner();
  },
  methods: {
    isAdmin() {
      this.isadmin = true;
      this.getToken()
      return this.role === "admin";
    },
    isKasir(){
      this.iskasir=true;
      this.getToken()
      return this.role === "kasir";
    },
    isOwner(){
      this.isowner=true;
      this.getToken()
      return this.role === "kasir";
    },
    // getToken() {
    //     this.token = localStorage.getItem("token");
    //     this.role = localStorage.getItem("role");
    // },
    // isAdmin() {
      // this.getToken()
    //   return this.role === "admin";
    // },
    getToken() {
      if (localStorage.getItem("token")) {
        this.token = localStorage.getItem("token");
        this.role = localStorage.getItem("role");
        // this.isAdmin();
        // this.$router.push("/transaksi");
      } else {
        console.log("NO TOKEN");
        // window.location = "/"; //coba
        this.$router.push("/");
      }
    },
    // isAdmin(prop) {
    //   // this.token = localStorage.getItem("token");
    //   this.role=prop.toLowerCase()
    //   if (this.role==="admin"){

    //   }
    // },
  },
};
</script>
