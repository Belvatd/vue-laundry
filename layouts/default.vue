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
            v-for="(item, i) in itemsAdmin"
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

    <div v-else-if="iskasir">
      <v-navigation-drawer
        v-if="iskasir"
        class="primary"
        v-model="drawer"
        :mini-variant="miniVariant"
        :clipped="clipped"
        fixed
        app
      >
        <v-list>
          <v-list-item
            v-for="(item, i) in itemsKasir"
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

    <div v-else-if="isowner">
      <v-navigation-drawer
        v-if="isowner"
        class="primary"
        v-model="drawer"
        :mini-variant="miniVariant"
        :clipped="clipped"
        fixed
        app
      >
        <v-list>
          <v-list-item
            v-for="(item, i) in itemsOwner"
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
      itemsAdmin: [
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
      itemsKasir: [
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
      ],
      itemsOwner:[
        {
          icon: "mdi-accounts-bubble",
          title: "Dashboard",
          to: "/home-owner",
        },
        {
          icon: "mdi-accounts-bubble",
          title: "Transaksi",
          to: "/transaksi",
        }
      ],
      miniVariant: false,
      right: false,
      rightDrawer: false,
      title: "Clean Laundry",
      token: "",
      role: "",
      reloading: false,
      isadmin: false,
      iskasir: false,
      isowner: false,
    };
  },

  mounted() {
    this.getToken();
    // this.reloadOnce();
  },
  methods: {
    getToken() {
      if (localStorage.getItem("token")) {
        this.token = localStorage.getItem("token");
        this.role = localStorage.getItem("role");
        console.log("masuk nii");
        if (this.role === "admin") {
          this.isadmin = true;
        } else if (this.role === "kasir") {
          this.iskasir = true;
        } else {
          this.isowner = true;
        }
      } else {
        console.log("NO TOKEN");
        this.$router.push("/");
      }
    },
    // reloadOnce() {
    //   this.reloading = localStorage.getItem("reloading");
    //   if (!this.reloading) {
    //     // localStorage.removeItem("reloading");
    //     // console.log("ppp");
    //     // window.location.reload();
    //     console.log("ga reload");
    //   } else {
    //     localStorage.removeItem("reloading");
    //     window.location.reload();
    //   }
    // },
  },
};
</script>
