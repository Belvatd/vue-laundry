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
        src="https://images.unsplash.com/photo-1569982175971-d92b01cf8694?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=735&q=80"
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
              <v-icon color="#fff">{{ item.icon }}</v-icon>
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
        <v-btn class="mr-4 primary" @click="logout"> Logout </v-btn>
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
        <v-btn class="mr-4 primary" @click="logout"> Logout </v-btn>
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
        <v-btn class="mr-4 primary" @click="logout"> Logout </v-btn>
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
          icon: "mdi-file-document-multiple",
          title: "Histori Transaksi",
          to: "/histori",
        },
        {
          icon: "mdi-account-multiple",
          title: "Daftar Member",
          to: "/list-member",
        },
        {
          icon: "mdi-home-city",
          title: "Daftar Outlet",
          to: "/list-outlet",
        },
        {
          icon: "mdi-archive",
          title: "Daftar Paket",
          to: "/list-paket",
        },
        {
          icon: "mdi-account-multiple",
          title: "Daftar User Admin",
          to: "/list-admin",
        },
        {
          icon: "mdi-account-multiple",
          title: "Daftar User Owner",
          to: "/list-owner",
        },
        {
          icon: "mdi-account-multiple",
          title: "Daftar User Kasir",
          to: "/list-kasir",
        },
      ],
      itemsKasir: [
        {
          icon: "mdi-apps",
          title: "Transaksi",
          to: "/transaksi",
        },
        {
          icon: "mdi-file-document-multiple",
          title: "Histori Transaksi",
          to: "/histori",
        },
        {
          icon: "mdi-accounts-multiple",
          title: "Daftar Member",
          to: "/list-member",
        },
      ],
      itemsOwner: [
        {
          icon: "mdi-home-city",
          title: "Dashboard",
          to: "/home-owner",
        },
        {
          icon: "mdi-apps",
          title: "Transaksi",
          to: "/transaksi",
        },
      ],
      miniVariant: false,
      right: false,
      rightDrawer: false,
      title: "Clean n Fresh",
      token: "",
      role: "",
      isadmin: false,
      iskasir: false,
      isowner: false,
    };
  },

  mounted() {
    this.getToken();
  },
  methods: {
    logout() {
      this.$router.push("/");
      localStorage.removeItem("role");
      localStorage.removeItem("token");
      localStorage.removeItem("user");
    },
    getToken() {
      if (localStorage.getItem("token")) {
        if (localStorage.getItem("role") === "admin") {
          this.isadmin = true;
        } else if (localStorage.getItem("role") === "kasir") {
          this.iskasir = true;
        } else {
          this.isowner = true;
        }
      } else {
        console.log("NO TOKEN");
        this.$router.push("/");
      }
    },
  },
};
</script>
