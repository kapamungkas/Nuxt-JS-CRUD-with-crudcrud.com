<template>
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
                label="First Name"
                required
                v-model="first_name"
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" md="6">
              <v-text-field
                label="Last Name"
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
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" text @click="closeDialog">
          Close
        </v-btn>
        <v-btn
          color="blue darken-1"
          text
          v-if="form_position === 'add'"
          @click="addUser()"
        >
          Add
        </v-btn>
        <v-btn
          color="blue darken-1"
          text
          v-if="form_position === 'edit'"
          @click="saveUser()"
        >
          Save
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>



<script>
export default {
  props: ["dialog", "form_position","id_selected"],
  data: () => ({
    first_name: "",
    last_name: "",
    email: "",
  }),

  methods: {
    clearForm() {
      this.first_name = "";
      this.last_name = "";
      this.email = "";
    },
    closeDialog(){
      this.$emit('closeDialog');
      this.clearForm()
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
          this.$parent.getListUser();
        })
        .catch((err) => {});
      this.$parent.dialog = false;
      this.clearForm()
    },

    saveUser() {
      let id = this.id_selected;
      this.$axios
        .$put("/" + id, {
          data: {
            first_name: this.first_name,
            last_name: this.last_name,
            email: this.email,
          },
        })
        .then((result) => {
          this.$parent.getListUser();
        })
        .catch((err) => {});
      this.$parent.dialog = false;
      this.clearForm()
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
  },

  watch: {
    id_selected: function (val) {
      if(val !== null){
        this.getUser(val)
      }
    },
  }
};
</script>
