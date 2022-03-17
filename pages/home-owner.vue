<template>
  <div id="app" v-if="isAdmin">
    <v-app id="list-admin">
      <v-data-table
        :headers="headers"
        :items="tableValues"
        sort-by="calories"
        class="elevation-1"
      >
        <template v-slot:top>
          <v-toolbar flat>
            <v-toolbar-title>Daftar Outlet</v-toolbar-title>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>
          </v-toolbar>
        </template>

        <template v-slot:[`item.actions`]="{ item }">
          <v-btn color="warning">
            <v-icon small @click="infoItem(item)">
              mdi-alert-circle-outline
            </v-icon>
          </v-btn>
        </template>
        <template v-slot:no-data>
          <p>No Data</p>
        </template>
      </v-data-table>
      <!-- Modal Info -->
      <v-dialog v-model="dialogInfo" max-width="500px">
        <v-card>
          <div id="print">
            <v-card-title>
              <span class="text-h5">Info Pembayaran</span>
            </v-card-title>
            <span class="infoTransaksi"
              >ID Outlet: {{ editedItem.id_outlet }}</span
            ><br />
            <span class="infoTransaksi">Alamat: {{ editedItem.alamat }}</span
            ><br />
            <span class="infoTransaksi"
              >Jumlah transaksi: {{ editedItem.jumlahTransaksi }}</span
            ><br />
          </div>
          <v-card-actions>
            <v-btn color="primary" class="white--text" @click="print"
              >Print</v-btn
            >
            <v-spacer></v-spacer>
            <v-btn color="primary" text @click="closeModal">Tutup</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
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
      {
        text: "ID",
        sortable: false,
        value: "id_outlet",
      },
      { text: "Alamat", value: "alamat" },
      { text: "Jumlah transaksi", value: "jumlahTransaksi" },
      { text: "Actions", value: "actions" },
    ],
    tableValues: [],
    editedIndex: -1,
    editedItem: {
      jenis: "",
      harga: "",
    },
    infoOutlet: {
      id_outlet: "",
      alamat: "",
      jumlahTransaksi: "",
    },
    isAdmin: false,
    notFound: false,
    data: {},
    token: "",
    actions: "",
    dialogInfo: false,
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "Tambah Paket" : "Edit Paket";
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
    print() {
      // Get HTML to print from element
      const prtHtml = document.getElementById("print").innerHTML;

      // Get all stylesheets HTML
      let stylesHtml = "";
      for (const node of [
        ...document.querySelectorAll('link[rel="stylesheet"], style'),
      ]) {
        stylesHtml += node.outerHTML;
      }

      // Open the print window
      const WinPrint = window.open("", "", "toolbar=0,scrollbars=0,status=0");

      WinPrint.document.write(`<!DOCTYPE html>
<html>
  <head>
    ${stylesHtml}
  </head>
  <body>
    ${prtHtml}
  </body>
</html>`);

      WinPrint.document.close();
      WinPrint.focus();
      WinPrint.print();
      WinPrint.close();
    },
    getToken() {
      if (localStorage.getItem("token")) {
        if (localStorage.getItem("role") === "owner") {
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
      let id_user = localStorage.getItem("id_user");
      let url = `http://localhost:8000/api/outlet/owner/` + id_user;
      await this.$axios
        .get(url, this.headerConfig())
        .then((res) => {
          res.data.data.forEach((element) => {
            this.tableValues.push(element);
          });
        })
        .catch((err) => {
          console.log(err);
        });
    },

    initialize() {},

    infoItem(item) {
      this.editedIndex = this.tableValues.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogInfo = true;
      console.log(this.editedItem, "pppp");
    },

    closeModal() {
      this.dialogInfo = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({});
        this.editedIndex = -1;
      });
    },
  },
};
</script>