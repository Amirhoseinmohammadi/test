<template>
  <div class="d-flex flex-column align-items-center">
    <div class="mx-2 my-3">
      <b-button size="sm" variant="success" class="mt-3" @click="addUser"
        >Add User</b-button
      >
    </div>
    <b-table
      class="w-100-sm mt-4"
      striped
      hover
      responsive
      :fields="fields"
      :items="items"
    >
      <template v-slot:cell(actions)="row">
        <b-button
          class="mr-4 w-100 p-2"
          @click="deleteUser(row.item)"
          variant="danger"
          >Delete</b-button
        >
        <b-button
          class="p-2 my-1 w-100"
          @click="editUser(row.item)"
          variant="info"
          >Edit</b-button
        >
      </template>
    </b-table>

    <b-modal v-model="modalShow" title="" no-footer>
      <b-form @submit.stop.prevent="submitForm">
        <b-form-group v-bind:lable-for="'edit-name'" lable="Name">
          <b-form-input
            v-model="form.name"
            :id="'edit-name'"
            required
          ></b-form-input>
        </b-form-group>
        <b-form-group v-bind:lable-for="'edit-usrname'" lable="Username">
          <b-form-input
            v-model="form.username"
            :id="'edit-username'"
            required
          ></b-form-input>
        </b-form-group>
        <b-form-group v-bind:lable-for="'edit-email'" lable="Email">
          <b-form-input
            v-model="form.email"
            :id="'edit-email'"
            required
          ></b-form-input>
        </b-form-group>
        <b-button type="submit" variant="primary">Submit</b-button>
        <b-button variant="danger" class="ml-3" @click="cancelEdit"
          >Cancel</b-button
        >
      </b-form>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      fields: ["name", "username", "email", "actions"],
      items: [],
      modalShow: false,
      form: {},
    };
  },
  mounted() {
    axios
      .get("https://jsonplaceholder.typicode.com/users")
      .then((response) => {
        this.items = response.data;
      })
      .catch((error) => {
        console.log(error);
      });
  },
  methods: {
    deleteUser(item) {
      axios
        .delete(`https://jsonplaceholder.typicode.com/users/${item.id}`)
        .then(() => {
          const index = this.items.indexOf(item);
          this.items.splice(index, 1);
        })
        .catch((error) => console.log(error));
    },

    addUser() {
      this.form = {};
      this.modalShow = true;
    },

    editUser(item) {
      this.form = { ...item };
      this.modalShow = true;
    },
    cancelEdit() {
      this.form = {};
      this.modalShow = false;
    },
    submitForm() {
      if (this.form.id) {
        axios
          .put(
            `https://jsonplaceholder.typicode.com/users/${this.form.id}`,
            this.form
          )
          .then((response) => {
            const index = this.items.findIndex(
              (item) => item.id === response.data.id
            );
            this.items.splice(index, 1, response.data);
            this.modalShow = false;
          })
          .catch((error) => {
            console.log(error);
          });
      } else {
        axios
          .post("https://jsonplaceholder.typicode.com/users", this.form)
          .then((response) => {
            this.items.push(response.data);
            this.modalShow = false;
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },
  },
};
</script>

<style lang="scss" scoped></style>
