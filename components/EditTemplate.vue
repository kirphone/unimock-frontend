<template>
  <v-card>
    <v-card-title>
      <span class="text-h5">{{ formTitle }}</span>
    </v-card-title>
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
    <v-card-text>
      <v-container>
        <v-row>
          <v-col
            cols="12"
          >
            <v-text-field
              v-model="item.name"
              label="Имя"
            ></v-text-field>
          </v-col>
        </v-row>
        <v-row>
          <v-col
            cols="12"
          >
            <v-textarea
              name="input-7-1"
              label="Тело"
              auto-grow
              v-model="item.body"
            ></v-textarea>
          </v-col>
        </v-row>
      </v-container>
    </v-card-text>
  </v-card>
</template>

<script>
export default {
  name: "EditTemplate",
  props: {
    editedItem: Object,
    formTitle: String
  },
  emits: ['save', 'close'],
  data() {
    return {
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
