<template>
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
              v-model="item.type"
              :items="supportedTypes"
              label="Тип"
              :menu-props="{ offsetY: true }"
            ></v-select>
          </v-col>
          <v-col
            cols="12"
            md="6"
          >
            <v-checkbox
              v-model="item.is_active"
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
              v-model="item.expression"
              label="Значение"
            ></v-text-field>
          </v-col>
          <v-col
            cols="12"
            md="6"
          >
            <v-text-field
              v-model="item.description"
              label="Описание"
            ></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col
            cols="12"
          >
            <v-text-field
              v-model="item.headers"
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
</template>

<script>
export default {
  name: "EditTrigger",
  props: {
    editedItem: Object,
    formTitle: String
  },
  emits: ['save', 'close'],
  data() {
    return {
      supportedTypes: ["gson", "jsonpath", "regex"],
      item: this.editedItem
    }
  },
  methods: {
    close() {
      this.$emit('close')
    },
    save() {
      this.$emit('save', this.item)
    }
  }
}
</script>

<style scoped>

</style>
