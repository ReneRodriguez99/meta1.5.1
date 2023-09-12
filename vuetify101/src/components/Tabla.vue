
<template>
  <v-data-table
    :headers="headers"
    :items="posts"
    :sort-by="[{ key: 'author', order: 'asc' }]"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar
        flat
      >
        <v-toolbar-title>Tabla CRUD</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-dialog
          v-model="dialog"
          max-width="500px"
        >
          <template v-slot:activator="{ props }">
            <v-btn
              color="primary"
              dark
              class="mb-2"
              v-bind="props"
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
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.name"
                      label="Autor"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.calories"
                      label="Titulo"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.fat"
                      label="Post"
                    ></v-text-field>
                  </v-col>
          
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="blue-darken-1"
                variant="text"
                @click="close"
              >
                Cancel
              </v-btn>
              <v-btn
                color="blue-darken-1"
                variant="text"
                @click="save"
              >
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="closeDelete">Cancel</v-btn>
              <v-btn color="blue-darken-1" variant="text" @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon
        size="small"
        class="me-2"
        @click="editItem(item.raw)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        size="small"
        @click="deleteItem(item.raw)"
      >
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn
        color="primary"
        @click="initialize"
      >
        Reset
      </v-btn>
    </template>
  </v-data-table>
</template>
  

<script>
export default {
  data: () => ({
    dialog: false,
    dialogDelete: false,
    headers: [
      {
        title: 'Autor',
        align: 'start',
        sortable: false,
        key: 'author',
      },
      { title: 'Titulo', key: 'title' },
      { title: 'Post', key: 'content' },
      { title: 'Actions', key: 'actions', sortable: false },
    ],
    posts: [],
    editedIndex: -1,
    editedItem: {
      author: '',
      title: '',
      content: '',
    },
    defaultItem: {
      author: '',
      title: '',
      content: '',
    },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'Nuevo Post' : 'Editar Post';
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

  methods: {
    async fetchData() {
      try {
        const usersResponse = await fetch("https://jsonplaceholder.typicode.com/users");
        const usersData = await usersResponse.json();

        const postsResponse = await fetch("https://jsonplaceholder.typicode.com/posts");
        const postsData = await postsResponse.json();

        
        this.posts = postsData.map((post) => ({
          author: usersData.find((user) => user.id === post.userId)?.name || "Unknown",
          title: post.title,
          content: post.body,
        }));
      } catch (error) {
        console.error("Error", error);
      }
    },

    initialize() {
      this.fetchData();
    },

    editItem(item) {
      this.editedIndex = this.posts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      this.editedIndex = this.posts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },

    deleteItemConfirm() {
      this.posts.splice(this.editedIndex, 1);
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

    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.posts[this.editedIndex], this.editedItem);
      } else {
        this.posts.push(this.editedItem);
      }
      this.close();
    },
  },
};
</script>