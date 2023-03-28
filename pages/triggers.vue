<template>
  <div>
    <v-card>
      <v-card-title>
        Триггеры
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
        :items="triggers"
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
                  Добавить триггер
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
                        md="6"
                      >
                        <v-select
                          v-model="editedItem.type"
                          :items="supportedTypes"
                          label="Тип"
                        ></v-select>
                      </v-col>
                      <v-col
                        cols="12"
                        md="6"
                      >
                        <v-checkbox
                          v-model="editedItem.is_active"
                          label="Активность"
                          color="success"
                        ></v-checkbox>
                      </v-col>
                    </v-row>
                    <v-row>
                      <v-col
                        cols="12"
                        md="6"
                      >
                        <v-text-field
                          v-model="editedItem.expression"
                          label="Значение"
                        ></v-text-field>
                      </v-col>
                      <v-col
                        cols="12"
                        md="6"
                      >
                        <v-text-field
                          v-model="editedItem.description"
                          label="Описание"
                        ></v-text-field>
                      </v-col>
                    </v-row>
                    <v-row>
                      <v-col
                        cols="12"
                      >
                        <v-text-field
                          v-model="editedItem.headers"
                          label="Заголовки"
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
                <v-card-title class="text-h5">Вы точно хотите удалить этот триггер?</v-card-title>
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
        <template v-slot:item.is_active="{ item }">
          <v-simple-checkbox
            :value="item.is_active"
            @input="changeActiveState(item, $event)"
          ></v-simple-checkbox>
        </template>
        <template #item.headers="{ item }">
          {{ convertHeadersToString(item.headers) }}
        </template>
      </v-data-table>
    </v-card>
  </div>
</template>

<script>
export default {
  name: "triggers",
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
        {text: 'Тип', value: 'type'},
        {text: 'Значение', value: 'expression'},
        {text: 'Описание', value: 'description'},
        {text: 'Заголовки', value: 'headers'},
        {text: 'Активность', value: 'is_active'},
        {text: 'Actions', value: 'actions', sortable: false },
      ],
      triggers: [],
      editedIndex: -1,
      editedItem: {
        id: 0,
        type: '',
        expression: '',
        description: '',
        headers: '',
        is_active: true
      },
      defaultItem: {
        id: 0,
        type: '',
        expression: '',
        description: '',
        headers: '',
        is_active: true
      },
      dialogDelete: false,
      dialog: false,
      supportedTypes: ["json", "regex"]
    }
  },
  methods: {
    async fetchTriggers() {
      this.loading = true
      this.$axios
        .$get(`${this.$config.unimockURL}/triggers?sorted=true`)
        .then((response) => {
          console.log("Get triggers")
          this.triggers = response
          this.loading = false
        })
        .catch((error) => {
          console.log(error);
        });
    },
    async updateTrigger(trigger) {
      return await this.$axios
        .$put(`${this.$config.unimockURL}/triggers/${trigger.id}`, trigger)
        .then(() => {
          console.log(`Update trigger with id ${trigger.id}`)
        })
        .catch((error) => {
          console.log(error);
        });
    },
    async saveTrigger(trigger) {
      return await this.$axios
        .$post(`${this.$config.unimockURL}/triggers`, trigger)
        .then((response) => {
          return response
        })
        .catch((error) => {
          console.log(error);
        });
    },
    async deleteTrigger(triggerId) {
      return await this.$axios
        .$delete(`${this.$config.unimockURL}/triggers/${triggerId}`)
        .then(() => {
          console.log(`Delete trigger with id ${triggerId}`)
        })
        .catch((error) => {
          console.log(error);
        });
    },
    convertHeadersToString(headers) {
      return headers === undefined ? "" : Object.entries(headers)
        .map(([key, value]) => `${key}=${value}`)
        .join(', ');
    },
    convertStringToHeaders(headersString) {
      const pairs = headersString.split(',');

      const resObj = {};

      for (const pair of pairs) {
        const kv = pair.trim().split('=', 2);
        if (kv.length === 2) {
          resObj[kv[0]] = kv[1];
        }
      }

      return resObj;
    },
    changeActiveState(trigger, newValue) {
      trigger.is_active = newValue
      this.updateTrigger(trigger).then(() => this.$set(trigger, 'is_active', newValue))
    },
    save() {
      this.editedItem.headers = this.convertStringToHeaders(this.editedItem.headers)
      if (this.editedIndex > -1) {
        this.updateTrigger(this.editedItem)
          .then(() => {
            Object.assign(this.triggers[this.editedIndex], this.editedItem)
            this.close()
          })
      } else {
        this.saveTrigger(this.editedItem)
          .then(response => {
            this.triggers.push(response)
            this.close()
          })
      }
    },
    editItem (item) {
      this.editedIndex = this.triggers.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.editedItem.headers = this.convertHeadersToString(item.headers)

      this.dialog = true
    },
    deleteItem (item) {
      this.editedIndex = this.triggers.indexOf(item)
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
    deleteItemConfirm () {
      this.deleteTrigger(this.editedItem.id).then(() => {
        this.triggers.splice(this.editedIndex, 1)
        this.closeDelete()
      })
    },
  },
  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'Добавить триггер' : 'Редактировать триггер'
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
    this.fetchTriggers()
  }
}
</script>

<style scoped>

</style>
