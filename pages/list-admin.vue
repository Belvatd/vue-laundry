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
            <v-toolbar-title>Daftar Admin</v-toolbar-title>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="500px">
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  color="primary"
                  dark
                  class="mb-2"
                  v-bind="attrs"
                  v-on="on"
                >
                  New Item
                </v-btn>
              </template>
              <v-card>
                <v-card-title>
                  <span class="text-h5">{{ formTitle }}</span>
                </v-card-title>

                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="editedItem.nama_user"
                          label="Nama"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="editedItem.username"
                          label="Username"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="editedItem.role"
                          label="Role"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="editedItem.password"
                          label="Password"
                        ></v-text-field>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-card-text>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="close">
                    Cancel
                  </v-btn>
                  <v-btn color="blue darken-1" text @click="update"> Save </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
            <v-dialog v-model="dialogDelete" max-width="500px">
              <v-card>
                <v-card-title class="text-h5"
                  >Are you sure you want to delete this item?</v-card-title
                >
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="closeDelete"
                    >Cancel</v-btn
                  >
                  <v-btn color="blue darken-1" text @click="deleteItemConfirm"
                    >OK</v-btn
                  >
                  <v-spacer></v-spacer>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-toolbar>
        </template>
        <template v-slot:[`item.actions`]="{ item }">
          <v-icon small class="mr-2" @click="editItem(item)">
            mdi-pencil
          </v-icon>
          <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
        </template>
        <template v-slot:no-data>
          <v-btn color="primary" @click="initialize"> Reset </v-btn>
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
    dialog: false,
    dialogDelete: false,
    headers: [
      {
        text: "ID",
        sortable: false,
        value: "id_user",
      },
      { text: "Nama", value: "nama_user", align: "start" },
      { text: "Username", value: "username" },
      { text: "Actions", value: "actions", sortable: false },
    ],
    tableValues: [],
    editedIndex: -1,
    editedItem: {
      nama_user: "",
      username: "",
      password: "",
      role: "",
    },
    defaultItem: {
      id: "",
      nama_user: "",
      username: "",
      role: "",
    },
    isAdmin: false,
    notFound: false,
    data: {},
    token: "",
    actions: "",
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "New Item" : "Edit Item";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },

  created() {
    this.initialize();
  },

  mounted() {
    this.getToken();
    this.getAdmin();
  },

  methods: {
    getToken() {
      if (localStorage.getItem("token")) {
        if (localStorage.getItem("role") === "admin") {
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
    async getAdmin() {
      let url = "http://localhost:8000/api/user";
      await this.$axios
        .get(url, this.headerConfig())
        .then((res) => {
          this.data = res.data;
          res.data.data.forEach((element) => {
            if (element.role == "admin") {
              try {
                console.log(element);
                this.tableValues.push(element);
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
    async update(e) {
      e.preventDefault();
      let data = {
        nama_user: this.editedItem.nama_user,
        username: this.editedItem.username,
        role: this.editedItem.role,
        password: this.editedItem.password,
      };
      // let form = new FormData();
      // form.append("nama_user", this.editedItem.nama_user);
      // form.append("username", this.editedItem.username);
      // form.append("role", this.editedItem.role);
      // form.append("password", this.editedItem.password);
      // form.append("id_petugas", values.id_petugas);
      await this.$axios
        .put(`http://localhost:8000/api/user/4`, data, this.headerConfig())
        .then(() => {
          // this.getAdmin();
          this.close();
          window.location.reload();
        })
        .catch((error) => {
          console.log(error);
        });
    },

    initialize() {
      // this.tableValues = [
      //   {
      //     id: "1",
      //     nama: "ayu",
      //     username: "ayu",
      //   },
      //   {
      //     id: "1",
      //     nama: "ayu",
      //     username: "ayu",
      //   },
      //   {
      //     id: "1",
      //     nama: "ayu",
      //     username: "ayu",
      //   },
      //   {
      //     id: "1",
      //     nama: "ayu",
      //     username: "ayu",
      //   },
      // ];
    },

    editItem(item) {
      this.editedIndex = this.tableValues.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      this.editedIndex = this.tableValues.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },

    deleteItemConfirm() {
      this.tableValues.splice(this.editedIndex, 1);
      this.closeDelete();
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    // save() {
    //   if (this.editedIndex > -1) {
    //     Object.assign(this.tableValues[this.editedIndex], this.editedItem);
    //   } else {
    //     this.tableValues.push(this.editedItem);
    //   }
    //   this.close();
    // },
  },
};
</script>