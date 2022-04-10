<template>
  <div>
    <div class="form-group">
      <label for="name">Name</label>
      <input
        type="text"
        id="name"
        placeholder="Enter name"
        class="form-control"
        v-model="item.name"
      />
    </div>

    <div class="form-group">
      <label for="phone">Phone</label>
      <input
        type="text"
        id="phone"
        placeholder="Enter phone"
        class="form-control"
        v-model="item.phone"
      />
    </div>
    <button class="btn btn-success mt-2" @click="save">
      {{ isEditing ? "Update" : "Save" }}
    </button>

    <div class="col-md-12" v-if="lists.length > 0">
      <h2 class="text-center">Telephone Numbers</h2>
      <ul class="list-group">
        <li class="list-group-item" v-for="item in lists" :key="item.id">
          <div class="d-flex justify-content-between align-items-center">
            {{ item.name }} - {{ item.phone }}
            <span>
              <button
                class="btn btn-warning btn-sm mr-2"
                @click="editTel(item)"
              >
                Edit
              </button>
              <button
                class="btn btn-danger btn-sm mr-2"
                @click="deleteTel(item.id)"
              >
                Delete
              </button>
            </span>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "Directory",

  data() {
    return {
      lists: [],
      item: {
        name: "",
        phone: "",
      },
      temp_id: null,
      isEditing: false,
    };
  },

  mounted() {
    this.fetchAll();
  },

  methods: {
    //   Fetch All data
    fetchAll() {
      axios.get("/api/tel").then((res) => {
        this.lists = res.data;
        // console.log(res.data);
      });
    },

    // Save Data
    save() {
      let method = axios.post;
      let url = "/api/tel";

      if (this.isEditing) {
        method = axios.put;
        url = `/api/tel/${this.temp_id}`;
      }

      try {
        method(url, this.item).then((res) => {
          this.fetchAll();
          this.item = {
            name: "",
            phone: "",
          };
          this.temp_id = null;
          this.isEditing = false;
        });
      } catch (error) {
        console.log(error);
      }
    },

    // Edit Data
    editTel(tel) {
      this.item = {
        name: tel.name,
        phone: tel.phone,
      };
      this.temp_id = tel.id;
      this.isEditing = true;
    },

    // Delete Data
    deleteTel(id) {
      try {
        axios.delete(`/api/tel/${id}`).then((res) => this.fetchAll());
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>

<style>
</style>
