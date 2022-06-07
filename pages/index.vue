<template>
  <section class="px-16">
    <v-row justify="center" align="center">
      <h1>Tasks ({{ qtdActiveTasks }})</h1>
    </v-row>
    <div class="d-flex my-6">
      <v-text-field
        v-model="description"
        class="rounded-0"
        placeholder="New task"
        dense
        outlined
        hide-details
      />
      <v-btn min-height="40" tile color="primary" @click="createTask">
        <v-icon>mdi-plus</v-icon>
        add
      </v-btn>
    </div>

    <v-list>
      <v-list-item
        v-for="(task, index) in tasks"
        :key="task.id"
        class="my-2 px-0"
      >
        <v-list-item-content
          class="px-2"
          :class="[
            task.active
              ? 'todo-task '
              : 'text-decoration-line-through done-task ',
          ]"
          @click="updateTask(task)"
        >
          <v-list-item-title>
            {{ task.description }}
          </v-list-item-title>
        </v-list-item-content>
        <v-list-item-action class="ma-0">
          <v-btn
            min-height="44"
            tile
            min-width="44"
            elevation="0"
            color="rgba(214, 97, 73, 100%)"
            @click="deleteTask(task, index)"
          >
            <v-icon small color="white">mdi-close</v-icon>
          </v-btn>
        </v-list-item-action>
      </v-list-item>
    </v-list>
  </section>
</template>

<script>
export default {
  name: 'IndexPage',
  data() {
    return {
      tasks: [],
      description: '',
      loading: false,
    }
  },
  computed: {
    qtdActiveTasks() {
      return this.tasks.filter((t) => t.active).length
    },
  },
  created() {
    this.getTasks()
  },
  methods: {
    async getTasks() {
      try {
        this.loading = true
        const response = await this.$axios.$get('http://localhost/api/tasks')
        this.tasks = response.data
      } catch (error) {
        if (error) {
          console.log(error)
        }
      } finally {
        this.loading = false
      }
    },
    async createTask() {
      try {
        this.loading = true
        const response = await this.$axios.$post('http://localhost/api/tasks', {
          description: this.description,
        })
        this.tasks.push(response.data)
        this.description = ''
      } catch (error) {
        if (error) {
          console.log(error)
        }
      } finally {
        this.loading = false
      }
    },
    async deleteTask(task, index) {
      try {
        this.loading = true
        await this.$axios.delete(`http://localhost/api/tasks/${task.id}`)
        this.tasks.splice(index, 1)
      } catch (error) {
        if (error) {
          console.log(error)
        }
      } finally {
        this.loading = false
      }
    },
    async updateTask(task) {
      try {
        this.loading = true
        await this.$axios.put(`http://localhost/api/tasks/${task.id}`, {
          active: !task.active,
        })
        task.active = !task.active
      } catch (error) {
        if (error) {
          console.log(error)
        }
      } finally {
        this.loading = false
      }
    },
  },
}
</script>
<style scoped>
.todo-task {
  background-color: rgba(240 240 240 / 100%);
}

.done-task {
  background-color: rgba(223 235 219 / 100%);
}
</style>
