<template>
  <div>
    <v-card>
      <v-card-title>
        Шаблоны
        <v-spacer></v-spacer>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Поиск"
          single-line
          hide-details
        ></v-text-field>
      </v-card-title>
      <v-data-table
        :headers="headers"
        :items="templates"
        :search="search"
        :loading="loading"
        loading-text="Идёт загрузка..."
        sort-by="id"
      >
        <template v-slot:top>
          <v-toolbar
            flat
          >
            <v-dialog
              v-model="dialog"
              max-width="500px"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  color="primary"
                  dark
                  class="mb-2"
                  v-bind="attrs"
                  v-on="on"
                >
                  Добавить шаблон
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
                      >
                        <v-text-field
                          v-model="editedItem.name"
                          label="Имя"
                        ></v-text-field>
                      </v-col>
                    </v-row>
                    <v-row>
                      <v-col
                        cols="12"
                      >
                        <v-text-field
                          v-model="editedItem.body"
                          label="Тело"
                        ></v-text-field>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-card-text>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn
                    color="blue darken-1"
                    text
                    @click="close"
                  >
                    Cancel
                  </v-btn>
                  <v-btn
                    color="blue darken-1"
                    text
                    @click="save"
                  >
                    Save
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
            <v-dialog v-model="dialogDelete" max-width="500px">
              <v-card>
                <v-card-title class="text-h5">Вы точно хотите удалить этот шаблон?</v-card-title>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
                  <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
                  <v-spacer></v-spacer>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-toolbar>
        </template>
        <template v-slot:item.actions="{ item }">
          <v-icon
            small
            class="mr-2"
            @click="editItem(item)"
          >
            mdi-pencil
          </v-icon>
          <v-icon
            small
            @click="deleteItem(item)"
          >
            mdi-delete
          </v-icon>
        </template>
      </v-data-table>
    </v-card>
  </div>
</template>

<script>
export default {
  name: "templates",
  data() {
    return {
      search: '',
      loading: true,
      headers: [
        {
          text: 'Id',
          align: 'start',
          value: 'id',
        },
        {text: 'Имя', value: 'name'},
        {text: 'Actions', value: 'actions', sortable: false},
      ],
      templates: [],
      dialogDelete: false,
      dialog: false,
      editedIndex: -1,
      editedItem: {
        id: 0,
        name: '',
        body: '',
      },
      defaultItem: {
        id: 0,
        name: '',
        body: '',
      },
    }
  },
  methods: {
    async fetchTemplates() {
      this.loading = true
      this.$axios
        .$get(this.$config.unimockURL + "/templates")
        .then((response) => {
          console.log("Get templates")
          this.templates = response
          this.loading = false
        })
        .catch((error) => {
          console.log(error);
        });
    },
    async updateTemplate(template) {
      return await this.$axios
        .$put(`${this.$config.unimockURL}/templates/${template.id}`, template)
        .then(() => {
          console.log(`Update template with id ${template.id}`)
        })
        .catch((error) => {
          console.log(error);
        });
    },
    async saveTemplate(template) {
      return await this.$axios
        .$post(`${this.$config.unimockURL}/templates`, template)
        .then((response) => {
          return response
        })
        .catch((error) => {
          console.log(error);
        });
    },
    async deleteTemplate(templateId) {
      return await this.$axios
        .$delete(`${this.$config.unimockURL}/templates/${templateId}`)
        .then(() => {
          console.log(`Delete template with id ${templateId}`)
        })
        .catch((error) => {
          console.log(error);
        });
    },
    save() {
      if (this.editedIndex > -1) {
        this.updateTemplate(this.editedItem)
          .then(() => {
            Object.assign(this.templates[this.editedIndex], this.editedItem)
            this.close()
          })
      } else {
        this.saveTemplate(this.editedItem)
          .then(response => {
            this.templates.push(response)
            this.close()
          })
      }
    },
    editItem(item) {
      this.editedIndex = this.templates.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialog = true
    },
    deleteItem(item) {
      this.editedIndex = this.templates.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialogDelete = true
    },
    close() {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },
    closeDelete() {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },
    deleteItemConfirm() {
      this.deleteTemplate(this.editedItem.id).then(() => {
        this.templates.splice(this.editedIndex, 1)
        this.closeDelete()
      })
    },
  },
  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'Добавить шаблон' : 'Редактировать шаблон'
    },
  },
  watch: {
    dialog(val) {
      val || this.close()
    },
    dialogDelete(val) {
      val || this.closeDelete()
    },
  },
  mounted() {
    this.fetchTemplates()
  }
}
</script>

<style scoped>

</style>
