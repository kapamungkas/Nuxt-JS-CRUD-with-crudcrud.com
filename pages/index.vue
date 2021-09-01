<template>
  <v-container class="lighten-5 mb-6">
    <v-row>
      <v-col md="4" offset-md="4">
        <v-card
          class="mx-auto mb-3"
          max-width="344"
          v-for="item in list_users"
          :key="item.id"
        >
          <!-- <v-img :src="item.avatar" height="200px"></v-img> -->

          <v-card-title>
            {{
              completeName(item.data.first_name, item.data.last_name)
            }}</v-card-title
          >

          <v-card-subtitle> {{ item.data.email }} </v-card-subtitle>

          <v-card-actions>
            <v-btn outlined text v-on:click="openForm(item._id)"> Edit </v-btn>
            <v-btn outlined text v-on:click="deleteUser(item._id)">
              Delete
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
      <v-col md="4">
        <v-fab-transition>
          <v-btn
            :key="activeFab.icon"
            :color="activeFab.color"
            fab
            large
            dark
            bottom
            right
            class="v-btn--example"
            v-on:click="openForm()"
          >
            <v-icon dark> mdi-account-plus </v-icon>
          </v-btn>
        </v-fab-transition>
      </v-col>
    </v-row>
    <v-row justify="center">
      <v-dialog v-model="dialog" persistent max-width="600px">
        <v-card>
          <v-card-title>
            <span class="text-h5">User Form</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12" sm="6" md="6">
                  <v-text-field
                    label="Legal first name*"
                    required
                    v-model="first_name"
                  ></v-text-field>
                </v-col>
                <v-col cols="12" sm="6" md="6">
                  <v-text-field
                    label="Legal middle name"
                    hint="example of helper text only on focus"
                    v-model="last_name"
                  ></v-text-field>
                </v-col>

                <v-col cols="12">
                  <v-text-field
                    label="Email*"
                    v-model="email"
                    required
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-container>
            <small>*indicates required field</small>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="dialog = false">
              Close
            </v-btn>
            <v-btn color="blue darken-1" text v-if="form_position === 'add'" @click="addUser()"> Add </v-btn>
            <v-btn color="blue darken-1" text v-if="form_position === 'edit'" @click="saveUser()"> Save </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-row>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    form_position: 'add',
    id_selected:'',
    list_users: null,
    fab: false,
    hidden: false,
    tabs: null,
    show: false,
    dialog: false,
    first_name: "",
    last_name: "",
    email: "",
  }),

  computed: {
    activeFab() {
      switch (this.tabs) {
        case "one":
          return { color: "success", icon: "mdi-share-variant" };
        case "two":
          return { color: "red", icon: "mdi-pencil" };
        case "three":
          return { color: "green", icon: "mdi-chevron-up" };
        default:
          return {};
      }
    },
  },
  methods: {
    completeName(firstName, lastName) {
      return firstName + " " + lastName;
    },
    clearForm(){
      this.first_name = '';
      this.last_name = '';
      this.email = '';
      this.form_position = 'add'
      this.id_selected = null
    },
    openForm(id = null) {
      this.clearForm()
      if (id != null) {
        this.getUser(id);
        this.form_position = 'edit'
        this.id_selected = id
      }
      this.dialog = !this.dialog;
    },
    getListUser() {
      this.$axios
        .$get("/")
        .then((result) => {
          this.list_users = result;
        })
        .catch((err) => {});
    },
    getUser(id) {
      this.$axios
        .$get("/" + id)
        .then((result) => {
          this.first_name = result.data.first_name;
          this.last_name = result.data.last_name;
          this.email = result.data.email;
        })
        .catch((err) => {});
    },
    addUser() {
      this.$axios
        .$post("/", {
          data: {
            first_name: this.first_name,
            last_name: this.last_name,
            email: this.email,
          },
        })
        .then((result) => {
          this.getListUser();
        })
        .catch((err) => {});
      this.dialog = false;
    },
    saveUser() {
      let id = this.id_selected
      this.$axios
        .$put("/"+id, {
          data: {
            first_name: this.first_name,
            last_name: this.last_name,
            email: this.email,
          },
        })
        .then((result) => {
          this.getListUser();
        })
        .catch((err) => {});
      this.dialog = false;
    },
    deleteUser(id) {
      this.$axios
        .$delete("/" + id)
        .then((result) => {
          this.getListUser();
        })
        .catch((err) => {});
    },
  },
  created: function () {
    // `this` points to the vm instance
    this.$axios
      .$get("/")
      .then((result) => {
        this.list_users = result;
      })
      .catch((err) => {});
  },
};
</script>
