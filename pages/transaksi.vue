<template>
  <div v-if="isAdmin">
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
        disabled
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
      <br />
      <v-btn class="mr-4 primary" @click="submit"> submit </v-btn>
    </v-form>

    <!-- Modal Bayar -->
    <v-dialog v-model="dialogPembayaran" max-width="500px">
      <v-card>
        <v-card-title>
          <span>Pembayaran</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <h3>Total Harga: Rp{{ infoTransaksi.totalBayar }}</h3>
            </v-row>
          </v-container>
        </v-card-text>
        <div class="actionModal">
          <v-spacer></v-spacer>
          <v-btn color="error" text @click="closeModal">Tutup</v-btn>
          <v-btn color="primary" text @click="bayar"> Bayar </v-btn>
        </div>
      </v-card>
    </v-dialog>
    <v-snackbar
      v-model="snackbar"
      :timeout="-1"
      :value="true"
      bottom
      color="primary"
      text
    >
      {{ message }}

      <template v-slot:action="{ attrs }">
        <v-btn color="primary" text v-bind="attrs" @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </div>
  <div v-else-if="notFound">
    <p>You're not allowed to access</p>
  </div>
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
      id_transaksi: "",
    },
    qty: 0,
    infoTransaksi: {
      id_transaksi: "",
      alamat: "",
      member: "",
      petugas: "",
      paket: "",
      harga: "",
      kuantitas: "",
      totalBayar: "",
      tanggalPesanan: "",
      batasWaktu: "",
      tanggalDibayar: "",
    },
    dialogPembayaran: false,
    isAdmin: false,
    notFound: false,
    message: "",
    snackbar: false,
  }),
  mounted() {
    this.getMember();
    this.getUser();
    this.getOutlet();
    this.getPaket();
    this.getToken();
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
        list_paket: [
          {
            id_paket: this.editedItem.id_paket,
            qty: this.qty,
          },
        ],
      };
      await this.$axios
        .post(url, data, this.headerConfig())
        .then((res) => {
          console.log(res);
          this.infoTransaksi.totalBayar = res.data.data.total;
          this.infoTransaksi.id_transaksi = res.data.data.id_transaksi;
          this.infoTransaksi.member = res.data.data.id_member;
          this.infoTransaksi.petugas = res.data.data.id_user;
          // this.infoTransaksi.paket = res.data.data.detail[0].paket.jenis;
          // this.infoTransaksi.harga = res.data.data.detail[0].paket.harga;
          this.infoTransaksi.kuantitas = res.data.data.detail[0].qty;
          this.infoTransaksi.tanggalPesanan = res.data.data.tgl_diterima;
          this.infoTransaksi.tanggalDibayar =
            res.data.data.tgl_bayar || "Belum dibayar";
          this.infoTransaksi.batasWaktu = res.data.data.batas_waktu;
          this.editedItem.id_transaksi = res.data.data.id_transaksi;
          this.message = res.data.message;
          this.snackbar = true;
          this.dialogPembayaran = true;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    async bayar() {
      let url =
        `http://localhost:8000/api/transaksi/bayar/` +
        this.editedItem.id_transaksi;
      await this.$axios
        .put(url, {}, this.headerConfig())
        .then(() => {
          this.closeModal();
          this.message = res.data.message;
          this.snackbar = true;
          window.location.reload();
        })
        .catch((err) => {
          console.log(err);
        });
    },
    closeModal() {
      this.dialogPembayaran = false;
    },
  },
};
</script>
