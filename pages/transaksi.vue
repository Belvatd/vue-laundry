<template>
  <v-form ref="form">
    <br />
    <h2 class="titlePage">Tambah Transaksi</h2>
    <br />

    <v-select
      label="Member"
      :items="member"
      item-value="id_member"
      item-text="nama_member"
      v-model="editedItem.id_member"
    >
    </v-select>

    <v-text-field
      v-model="user.nama_user"
      
      label="Petugas"
      required
    ></v-text-field>

    <v-select
      label="Outlet"
      :items="outlet"
      item-text="alamat"
      item-value="id_outlet"
      v-model="editedItem.id_outlet"

    >
    </v-select>
    <v-select
      label="Daftar Paket"
      :items="paket"
      item-value="id_paket"
      item-text="jenis"
      v-model="editedItem.id_paket"

    >
    </v-select>

    <v-text-field v-model="qty" label="Banyak (Kg)" required></v-text-field>

    <!-- <v-text-field v-model="tanggal" label="Tanggal" required></v-text-field> -->

    <!-- <v-text-field v-model="batas" label="Batas" required></v-text-field> -->

    <!-- <v-select
      v-model="select"
      :items="items"
      :rules="[(v) => !!v || 'Item is required']"
      label="Status"
      required
    ></v-select> -->
    <br />
    <v-btn class="mr-4 primary" @click="submit"> submit </v-btn>
  </v-form>
</template>

<script>
export default {
  data: () => ({
    valid: true,
    member: [],
    user: [
      {
        id_user: "",
        nama_user: "",
      },
    ],
    outlet: [],
    paket: [],
    editedItem: {
      id_user: "",
      id_member: "",
      id_outlet: "",
      id_paket: "",
    },
    qty: 0,
    // select: null,
    // items: ["Baru", "Proses", "Selesai", "Diambil"],
  }),
  mounted() {
    this.getMember();
    this.getUser();
    this.getOutlet();
    this.getPaket();
  },
  methods: {
    headerConfig() {
      let header = {
        headers: { Authorization: `Bearer ${localStorage.getItem("token")}` },
      };
      return header;
    },
    async getMember() {
      let url = "http://localhost:8000/api/member";
      await this.$axios
        .get(url, this.headerConfig())
        .then((res) => {
          this.member = res.data.data;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    async getUser() {
      let url = "http://localhost:8000/api/user";
      await this.$axios
        .get(url, this.headerConfig())
        .then((res) => {
          res.data.data.forEach((element) => {
            if (element.id_user == localStorage.getItem("id_user")) {
              try {
                // this.user = element.nama_user
                // this.user.push(element);
                this.user.id_user = element.id_user;
                this.user.nama_user = element.nama_user;
                console.log(element.nama_user);
              } catch (error) {
                console.log(error);
              }
            }
          });
        })
        .catch((err) => {
          console.log(err);
        });
    },
    async getOutlet() {
      let url = "http://localhost:8000/api/outlet";
      await this.$axios
        .get(url, this.headerConfig())
        .then((res) => {
          this.outlet = res.data.data.map((val) => ({
            id_user: val.id_user,
            id_outlet: val.id_outlet,
            nama_user: val.user.nama_user,
            alamat: val.alamat,
          }));
        })
        .catch((err) => {
          console.log(err);
        });
    },
    async getPaket() {
      let url = "http://localhost:8000/api/paket";

      await this.$axios
        .get(url, this.headerConfig())
        .then((res) => {
          this.paket = res.data.data;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    async submit() {
      let url = "http://localhost:8000/api/transaksi";
      let data = {
        id_member: this.editedItem.id_member,
        id_user: parseInt(localStorage.getItem("id_user")),
        id_outlet: this.editedItem.id_outlet,
        list_paket: [{
          id_paket: this.editedItem.id_paket,
          qty: this.qty,
        }],
      };
      await this.$axios
        .post(url, data, this.headerConfig())
        .then((res) => {
          console.log(res);
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>
