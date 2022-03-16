<template>
  <div id="app" v-if="isAdmin">
    <v-app id="list-admin">
      <v-data-table
        :headers="headers"
        :items="items"
        sort-by="calories"
        class="elevation-1"
      >
        <template v-slot:top>
          <v-toolbar flat>
            <v-toolbar-title>Histori Transaksi</v-toolbar-title>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>
          </v-toolbar>
        </template>
        <template v-slot:no-data>
          <p>No Data</p>
        </template>
      </v-data-table>
    </v-app>
  </div>
  <div v-else-if="notFound">
    <p>You're not allowed to access</p>
  </div>
</template>
<script>
export default {
  data: () => ({
    dialogTambah: false,
    dialogDelete: false,
    dialogEdit: false,
    jenis_kelamin: ["perempuan", "laki-laki"],
    headers: [
      { text: "ID", value: "id_transaksi" },
      { text: "Member", value: "nama_member" },
      { text: "Outlet", value: "alamat" },
      { text: "Petugas", value: "nama_user" },
      { text: "Batas Waktu", value: "batas_waktu" },
      { text: "Tanggal Bayar", value: "tgl_bayar" },
    ],
    tableValues: [],
    editedIndex: -1,
    editedItem: {
      jenis: "",
      harga: "",
    },
    isAdmin: false,
    notFound: false,
    data: {},
    token: "",
    actions: "",
    items: [],
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "Tambah Member" : "Edit Member";
    },
  },

  watch: {
    dialogTambah(val) {
      val || this.closeTambah();
    },
    dialogEdit(val) {
      val || this.closeEdit();
    },
  },

  created() {
    this.initialize();
  },

  mounted() {
    this.getToken();
    this.getUser();
  },

  methods: {
    getToken() {
      if (localStorage.getItem("token")) {
        if (
          localStorage.getItem("role") === "admin" ||
          localStorage.getItem("role") === "kasir"
        ) {
          this.isAdmin = true;
        } else {
          this.notFound = true;
        }
      } else {
        console.log("NO TOKEN");
        this.$router.push("/");
      }
    },
    headerConfig() {
      let header = {
        headers: { Authorization: `Bearer ${localStorage.getItem("token")}` },
      };
      return header;
    },
    async getUser() {
      let url = "http://localhost:8000/api/transaksi";
      await this.$axios
        .get(url, this.headerConfig())
        .then((res) => {
          this.items = res.data.data.map((val) => ({
            id_transaksi: val.id_transaksi,
            nama_member: val.member.nama_member,
            nama_user: val.user.nama_user,
            alamat: val.outlet.alamat,
            batas_waktu: val.batas_waktu,
            tgl_bayar: val.tgl_bayar,
          }));
        })
        .catch((err) => {
          console.log(err);
        });
    },

    async update(e) {
      e.preventDefault();
      let data = {
        jenis: this.editedItem.jenis,
        harga: this.editedItem.harga,
      };
      await this.$axios
        .put(
          `http://localhost:8000/api/paket/` + this.editedItem.id_paket,
          data,
          this.headerConfig()
        )
        .then(() => {
          this.closeEdit();
          window.location.reload();
        })
        .catch((error) => {
          console.log(error);
        });
    },

    async add() {
      let url = `http://localhost:8000/api/paket`;
      let checkData = {
        jenis: this.editedItem.jenis,
        harga: this.editedItem.harga,
      };
      await this.$axios
        .post(url, checkData, this.headerConfig())
        .then((res) => {
          this.getUser();
          window.location.reload();
        })
        .catch((err) => {
          console.log(err);
        });
    },
    tambahItem() {
      this.dialogTambah = true;
    },

    closeTambah() {
      this.dialogTambah = false;
    },

    initialize() {},

    editItem(item) {
      this.editedIndex = this.tableValues.indexOf(item);
      this.editedItem = Object.assign({}, item);
      console.log(this.editedItem);
      this.dialogEdit = true;
    },

    closeEdit() {
      this.dialogEdit = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({});
        this.editedIndex = -1;
      });
    },

    deleteItem(item) {
      this.editedIndex = this.tableValues.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },
  },
};
</script>