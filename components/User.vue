<template lang="">
  <div>
    <v-container class="lighten-5 mb-6">
    <v-row>
      <v-col md="4" offset-md="4">
        <UsersCardList :list="list_users">
        </UsersCardList>
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
      <UsersDialog :dialog="dialog" :form_position="form_position" :id_selected="id_selected" @closeDialog="closeDialog">
      </UsersDialog>
    </v-row>
  </v-container>
  </div>
</template>

<script>
export default {
  data: () => ({
    form_position: "add",
    id_selected: "",
    list_users: null,
    fab: false,
    hidden: false,
    tabs: null,
    show: false,
    dialog: false,
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
    closeDialog(){
      this.dialog = !this.dialog;
    },
    openForm(id = null) {
      if (id != null) {
        this.form_position = "edit";
        this.id_selected = id;
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

