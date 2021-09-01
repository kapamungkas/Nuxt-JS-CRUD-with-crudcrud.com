<template>
  <div>
    <v-card
      class="mx-auto mb-3"
      max-width="344"
      v-for="item in list"
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
        <v-btn outlined text v-on:click="deleteUser(item._id)"> Delete </v-btn>
      </v-card-actions>
    </v-card>
  </div>
</template>

<script>
export default {
  props: ["list"],
  methods: {
    completeName(firstName, lastName) {
      return firstName + " " + lastName;
    },
    openForm(id = null) {
      this.$parent.openForm(id);
    },
    deleteUser(id = null) {
      this.$parent.deleteUser(id);
    },
  },
};
</script>
