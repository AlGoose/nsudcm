<template>
<div class="table">
  <v-toolbar flat color="white">
    <v-toolbar-title>My Employees</v-toolbar-title>
    <v-divider class="mx-2" inset vertical></v-divider>
    <v-spacer></v-spacer>
    <v-text-field v-model="search" append-icon="search" label="Search" single-line hide-details></v-text-field>
    <v-dialog v-model="dialog" max-width="500px">
      <template v-slot:activator="{ on }">
        <v-btn color="primary" dark class="mb-2" v-on="on">Add</v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">{{ formTitle }}</span>
        </v-card-title>

        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs6>
                <v-text-field v-model="editedItem.surname" label="Фамилия"></v-text-field>
              </v-flex>
              <v-flex xs6>
                <v-text-field v-model="editedItem.name" label="Имя"></v-text-field>
              </v-flex>
              <v-flex xs6>
                <v-text-field v-model="editedItem.patronymic" label="Отчество"></v-text-field>
              </v-flex>
              <v-flex xs6>
                <v-text-field v-model="editedItem.position" label="Должность"></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-text-field v-model="editedItem.email" label="Почта"></v-text-field>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click="close">Cancel</v-btn>
          <v-btn color="blue darken-1" flat @click="save">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-toolbar>

  <v-data-table :headers="headers" :items="desserts" :search="search" class="elevation-1">
    <template v-slot:items="props">
      <td>{{ props.item.surname }}</td>
      <td class="text-xs-left">{{ props.item.name }}</td>
      <td class="text-xs-left">{{ props.item.patronymic }}</td>
      <td class="text-xs-left">{{ props.item.position }}</td>
      <td class="text-xs-left">{{ props.item.email }}</td>
      <td class="justify-left layout px-0">
        <v-icon small class="mr-2" @click="editItem(props.item)">edit</v-icon>
        <v-icon small @click="deleteItem(props.item)">delete</v-icon>
      </td>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize">Reset</v-btn>
    </template>
  </v-data-table>
</div>
</template>

<script>
export default {
  data: () => ({
    search: '',
    dialog: false,
    headers: [{
        text: 'Фамилия',
        value: 'surname'
      },
      {
        text: 'Имя',
        value: 'name'
      },
      {
        text: 'Отчество',
        sortable: false,
        value: 'patronymic'
      },
      {
        text: 'Должность',
        value: 'position'
      },
      {
        text: 'Почта',
        sortable: false,
        value: 'email'
      },
      {
        text: 'Actions',
        value: 'surname',
        sortable: false
      }
    ],
    desserts: [],
    editedIndex: -1,
    editedItem: {
      surname: '',
      name: '',
      patronymic: '',
      position: '',
      email: ''
    },
    defaultItem: {
      surname: '',
      name: '',
      patronymic: '',
      position: '',
      email: ''
    }
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
    }
  },

  watch: {
    dialog(val) {
      val || this.close()
    }
  },

  created() {
    this.initialize()
  },

  methods: {
    initialize() {
      this.desserts = [{
          surname: 'Frozen Yogurt',
          name: 159,
          patronymic: 6.0,
          position: 24,
          email: 4.0
        },
        {
          surname: 'Ice cream sandwich',
          name: 237,
          patronymic: 9.0,
          position: 37,
          email: 4.3
        }
      ]
    },

    editItem(item) {
      this.editedIndex = this.desserts.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialog = true
    },

    deleteItem(item) {
      const index = this.desserts.indexOf(item)
      confirm('Are you sure you want to delete this item?') && this.desserts.splice(index, 1)
    },

    close() {
      this.dialog = false
      setTimeout(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      }, 300)
    },

    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.desserts[this.editedIndex], this.editedItem)
      } else {
        this.desserts.push(this.editedItem)
      }
      this.close()
    }
  }
}
</script>

<style>
  .table {
    margin-left: 10%;
    width: 80%;
  }
</style>
