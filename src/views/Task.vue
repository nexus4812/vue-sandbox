<template>
  <div>
    <h2>Todolist</h2>

    <form v-on:submit.prevent>
      <p>
        <label>
          <input type="text" v-model="newItem.title" />
          <button v-on:click="addTask">Add</button>
        </label>
      </p>
    </form>

    <ul style="list-style: none">
      <li
        v-for="task in tasks"
        v-bind:key="task.id"
        v-bind:class="{ completed: task.isCompleted }"
      >
        {{ task.title }}
        <label>
          <input type="checkbox" v-model="task.isCompleted" />
        </label>

        <button v-on:click="deleteTask(task.id)">Delete</button>
      </li>
    </ul>
  </div>

  <!--  <pre>{{ $data }}</pre>-->
</template>

<script lang="ts">
type task = {
  id: number;
  title: string;
  isCompleted: boolean;
};

type data = {
  newItem: task;
  tasks: task[];
};

export default {
  name: "Task",
  data: (): data => ({
    newItem: {
      id: 1,
      title: "",
      isCompleted: false,
    },
    tasks: [],
  }),

  methods: {
    addTask: function (this: data): void {
      if (this.newItem.title === "") {
        return;
      }
      this.tasks.push(Object.assign({}, this.newItem));
      this.newItem.title = "";
      this.newItem.id += 1;
    },

    deleteTask: function (this: data, id: number): void {
      this.tasks = this.tasks.filter((task: task) => task.id !== id);
    },
  },
};
</script>

<style scoped>
.completed {
  text-decoration: line-through;
}
</style>
