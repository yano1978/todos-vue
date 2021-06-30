<style lang="scss">
@import "./assets/styles/scss/main.scss";
</style>
<template>
  <header>
    <div class="navbar shadow-sm">
      <div class="container d-flex justify-content-between">
        <a href="#" class="navbar-brand d-flex align-items-center">
          <h5>
            Click-Bait Industries
          </h5>
        </a>
          <img class="avatar" src="https://i.pravatar.cc/50" alt="random-avatar" />
      </div>
    </div>
  </header>
  <form @submit.prevent="addTodo()">
    <label>Add a task</label>
    <textarea
      v-model="newTodo"
      class="add-todo"
      name="newTodo"
      autocomplete="off"
      :placeholder="placeholder"
    />
    <button class="add-btn"><i class="fa fa-plus"></i>Add a task</button>
  </form>
  <nav>
    <div class="nav nav-tabs" id="nav-tab" role="tablist">
      <button
        class="nav-link active"
        id="nav-home-tab"
        data-bs-toggle="tab"
        data-bs-target="#nav-home"
        type="button"
        role="tab"
        aria-controls="nav-home"
        aria-selected="true"
      >
        To do list
      </button>
      <button
        class="nav-link"
        id="nav-profile-tab"
        data-bs-toggle="tab"
        data-bs-target="#nav-profile"
        type="button"
        role="tab"
        aria-controls="nav-profile"
        aria-selected="false"
      >
        Recent
      </button>
    </div>
  </nav>
  <div class="tab-content" id="nav-tabContent">
    <div
      class="tab-pane fade show active"
      id="nav-home"
      role="tabpanel"
      aria-labelledby="nav-home-tab"
    >
      <ul>
        <li
          class="todo done"
          v-for="item of done"
          :key="item.id"
          @click="toggle(item)"
        >
          <label class="wrapper">
            <input type="checkbox" />
            <span class="checkmark"></span>
          </label>
          <span>{{ item.content }}</span>
        </li>
      </ul>
      <div v-if="!done.length" key="undone" class="empty done">
        No done todos
      </div>
    </div>
    <div
      class="tab-pane fade"
      id="nav-profile"
      role="tabpanel"
      aria-labelledby="nav-profile-tab"
    >
      <ul>
        <li
          class="todo undone"
          v-for="(item, index) of undone"
          :key="item.id"
          :style="{ 'grid-row-start': index + 1 }"
          @click="toggle(item)"
        >
          <label class="wrapper">
            <input type="checkbox" checked />
            <span class="checkmark"></span>
          </label>
          <span>{{ item.content }}</span>
        </li>
      </ul>
      <div v-if="!undone.length" key="undone" class="empty undone">
        No undone todos
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
export default {
  name: "App",
  created: function() {
    document.body.style.backgroundColor = "#27292d";
    document.body.style.color = "#fff";
  },
  unmounted: function() {
    document.body.style.backgroundColor = null;
    document.body.style.color = null;
  },

  computed: {
    done() {
      return this.todos.filter((i) => i.done);
    },

    undone() {
      return this.todos.filter((i) => !i.done);
    },
  },

  methods: {
    toggle(todo) {
      todo.done = !todo.done;
    },
  },
  data() {
    return {
      text: "",
      placeholder: "Enter your task",
    };
  },
  setup() {
    const newTodo = ref("");
    const TodoList = [
      { content: "Create a report", done: true },
      { content: "Create UI", done: true },
      { content: "Create a user and wire flow", done: true },
      { content: "Create wireframes", done: true },
      { content: "Organise a handover with the developers", done: true },
      { content: "Create workshop", done: true },
      { content: "Book client kick off meeting", done: false },
      { content: "Send tender", done: false },
    ];
    const todosData = JSON.parse(localStorage.getItem("todos")) || TodoList;
    const todos = ref(todosData);
    function addTodo() {
      if (newTodo.value) {
        todos.value.push({
          done: true,
          content: newTodo.value,
        });
        newTodo.value = "";
      }
      saveData();
    }

    function doneTodo(todo) {
      todo.done = !todo.done;
      saveData();
    }

    // function randomDate(start, end) {
    //   return new Date(
    //     start.getTime() + Math.random() * (end.getTime() - start.getTime())
    //   );
    // }
    // date: randomDate(new Date(2012, 0, 1), new Date())

    // function format_time(s) {
    //   const dtFormat = new Intl.DateTimeFormat("en-GB", {
    //     timeStyle: "medium",
    //     timeZone: "UTC",
    //   });

    //   return dtFormat.format(new Date(s * 1e3));
    // }

    function saveData() {
      const storageData = JSON.stringify(todos.value);
      localStorage.setItem("todos", storageData);
    }

    return {
      todos,
      newTodo,
      addTodo,
      doneTodo,
      saveData,
    };
  },
};
</script>
