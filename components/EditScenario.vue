<template>
  <v-card>
    <v-card-title>
      <span class="text-h5">Редактирование сценария</span>
    </v-card-title>

    <v-card-text>
      <v-container>
        <table class="table table-striped">
          <thead>
          <tr>
            <th scope="col">Номер</th>
            <th scope="col">Тип</th>
            <th scope="col">Значение</th>
          </tr>
          </thead>
          <draggable
            v-model="steps"
            class="list-group"
            ghost-class="ghost"
            @start="drag = true"
            @end="endMove"
            v-bind="dragOptions"
            tag="tbody"
          >
            <tr v-for="element in steps" :key="element.order_number" class="list-group-item">
              <td>{{ element.order_number }}</td>
              <td>
                <v-select
                  v-model="element.step_type"
                  :items="supportedTypes"
                  light
                ></v-select>
              </td>
              <td>
                <v-text-field
                  v-model.number="element.value"
                  light
                ></v-text-field>
              </td>
            </tr>
          </draggable>
        </table>
      </v-container>
    </v-card-text>

    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn
        color="primary"
        @click="addStep"
      >
        Добавить шаг
      </v-btn>
      <v-btn
        @click="close"
        depressed
        color="error"
      >
        Закрыть
      </v-btn>
      <v-btn
        depressed
        color="green darken-2"
        @click="saveStepsForTrigger"
        class="ma-2"
      >
        Сохранить
      </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  name: "EditScenario",
  props: {
    triggerId: Number
  },
  emits: ['close'],
  data() {
    return {
      steps: [],
      drag: false,
      supportedTypes: ["template_processing", "delay"],
      defaultItem: {
        id: -1,
        order_number: 0,
        value: 0,
        trigger_id: this.triggerId,
        step_type: ''
      },
    }
  },
  methods: {
    close() {
      this.$emit('close')
    },
    async fetchScenarioForTrigger() {
      this.$axios
        .$get(`${this.$config.unimockURL}/steps/field/triggerId/${this.triggerId}`)
        .then((response) => {
          console.log(`Get steps for trigger id = ${this.triggerId}`)
          this.steps = response
        })
        .catch((error) => {
          console.log(error);
        });
    },
    endMove() {
      this.drag = false

      this.steps.forEach((obj, index) => {
        obj.order_number = index
      });
    },
    async addStep() {
      let newItem = Object.assign({}, this.defaultItem)
      newItem.order_number = this.steps.length
      newItem.step_type = this.supportedTypes[0]
      this.steps.push(newItem)
    },
    async saveStepsForTrigger() {
      this.$axios
        .put(`${this.$config.unimockURL}/steps/field/triggerId/${this.triggerId}`, this.steps)
        .then((response) => {
          this.steps = response.data
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  computed: {
    dragOptions() {
      return {
        animation: 200,
        group: "description",
        disabled: false,
        ghostClass: "ghost"
      };
    }
  },
  mounted() {
    this.fetchScenarioForTrigger()
  }
}
</script>

<style scoped>
.list-group {
  background-color: white;
  color: black;
}

.list-group-item {
  border: black solid 1px;
  padding: 10px;
  cursor: move;
  list-style-type: none;
}

.list-group-item i {
  cursor: pointer;
}

.ghost {
  opacity: 0.5;
  background: #c8ebfb;
}

.table td, th {
  padding: 5px 15px;
  border: black solid 1px;
  background: white;
  color: black;
}
</style>
