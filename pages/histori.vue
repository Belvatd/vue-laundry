<template>
  <div id="app" v-if="isAdmin">
    <v-app id="list-admin">
      <v-data-table :headers="headers" :items="items" class="elevation-1">
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
        <template v-slot:[`item.actions`]="{ item }">
          <v-icon small class="mr-2" @click="editItem(item)">
            mdi-pencil
          </v-icon>
          <v-icon small class="mr-2" @click="editBayar(item)">
            mdi-alert-circle-outline
          </v-icon>
        </template>
      </v-data-table>

      <!-- modal edit status pesanan -->
      <v-dialog v-model="dialogEdit" max-width="500px">
        <v-card>
          <v-card-title>
            <span class="text-h5">Edit Status Pesanan</span>
          </v-card-title>
          <v-card-text>
            <v-col cols="12" sm="6" md="4">
              <v-select
                :items="status"
                v-model="editedItem.status"
                label="Status Pesanan"
              >
              </v-select>
            </v-col>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="closeEdit">Tutup</v-btn>
            <v-btn color="blue darken-1" text @click="updateStatus"
              >Simpan</v-btn
            >
          </v-card-actions>
        </v-card>
      </v-dialog>

      <v-dialog v-model="dialogPembayaran" max-width="500px">
        <v-card>
          <v-card-title>
            <span class="text-h5">Info Pembayaran</span>
          </v-card-title>
          <span class="text-h5">Total Harga: Rp{{ editedItem.totalBayar }}</span>

          <!-- <v-card-text>
            <v-col cols="12" sm="6" md="4">
              <v-select
                :items="status"
                v-model="editedItem.status"
                label="Status Pesanan"
              >
              </v-select>
            </v-col>
          </v-card-text> -->
          <v-text-field v-model="totalBayar" label="" required></v-text-field>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="closeEdit">Tutup</v-btn>
            <v-btn color="blue darken-1" text @click="updateStatus"
              >Simpan</v-btn
            >
          </v-card-actions>
        </v-card>
      </v-dialog>
      <!-- Modal Bayar -->
      <!-- <v-dialog v-model="dialogPembayaran" max-width="500px">
        <v-card>
          <v-card-title>
            <span class="text-h5">Pembayaran</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                <span class="text-h5">Total Harga: Rp{{ totalBayar }}</span>
              </v-row>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="closeModal">Tutup</v-btn>
            <v-btn color="blue darken-1" text @click="bayar" v-if="isPaid"> Bayar </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog> -->
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
      { text: "Status", value: "status" },
      { text: "Actions", value: "actions" },
    ],
    tableValues: [],
    editedIndex: -1,
    editedItem: {
      id_transaksi: "",
      totalBayar: "",
      status: "",
    },
    status: ["baru", "diproses", "selesai"],
    isAdmin: false,
    notFound: false,
    data: {},
    token: "",
    actions: "",
    items: [],
    totalBayar: "",
    dialogEdit: false,
    dialogPembayaran: false,
    isPaid: true,
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
    // this.getDetailTransaksi();
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
            status: val.status,
          }));
        })
        .catch((err) => {
          console.log(err);
        });
    },
    async getDetailTransaksi(id) {
      let url = `http://localhost:8000/api/transaksi/`+ id;
      await this.$axios
        .get(url, this.headerConfig())
        .then((res) => {
          this.totalBayar = res.data.data.total;
        })
        .catch((err) => {
          console.log(err);
        });
    },

    async updateStatus(e) {
      e.preventDefault();
      let data = {
        status: this.editedItem.status,
      };
      await this.$axios
        .put(
          `http://localhost:8000/api/transaksi/status/` +
            this.editedItem.id_transaksi,
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

    async bayar() {
      let url =
        `http://localhost:8000/api/transaksi/bayar/` +
        this.editedItem.id_transaksi;
      await this.$axios
        .put(url, {}, this.headerConfig())
        .then(this.closeModal())
        .catch((err) => {
          console.log(err);
        });
    },

    initialize() {},

    editItem(item) {
      this.editedIndex = this.tableValues.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogEdit = true;
    },

    editBayar(item) {
      this.editedIndex = this.tableValues.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.getDetailTransaksi(this.editedItem.id_transaksi)
      // console.log(this.editedItem.id_transaksi)

      this.dialogPembayaran = true;
    },

    closeEdit() {
      this.dialogEdit = false;
    },
  },
};
</script>