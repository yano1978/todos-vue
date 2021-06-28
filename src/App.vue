<template>
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
    <form @submit.prevent="addTodo()">
      <label>Add a task</label>
      <input
        v-model="newTodo"
        name="newTodo"
        autocomplete="off"
        :placeholder="placeholder"
      />
      <button>Add a task</button>
    </form>
    <div
      class="tab-pane fade show active"
      id="nav-home"
      role="tabpanel"
      aria-labelledby="nav-home-tab"
    >
      <ul>
        <li v-for="(todo, index) in todos" :key="index">
          <label class="wrapper">
            <input
              :class="{ done: todo.done }"
              @click="doneTodo(todo)"
              type="checkbox"
            />
            <span class="checkmark"></span>
          </label>
          <span :class="{ done: todo.done }" @click="doneTodo(todo)">{{
            todo.content
          }}</span>
          <span>{{ todo.date }}</span>
          <button @click="removeTodo(index)">Remove</button>
        </li>
      </ul>
      <h4 v-if="todos.length === 0">Empty list.</h4>
    </div>
    <div
      class="tab-pane fade"
      id="nav-profile"
      role="tabpanel"
      aria-labelledby="nav-profile-tab"
    >
      <ul>
        <li v-for="(recent, index) in recents" :key="index">
          <span>{{ recent.content }}</span>
        </li>
      </ul>
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
  data() {
    return {
      text: "",
      placeholder: "Enter your task",
    };
  },
  setup() {
    const newTodo = ref("");
    const TodoList = [
      {
        done: false,
        content: "Create a report",
        date: format_time(12345)
      },
      {
        done: false,
        content: "Create UI",
        date: format_time(72344)
        // date: randomDate(new Date(2012, 0, 1), new Date()),
      },
      {
        done: false,
        content: "Create user and wire flow",
        date: format_time(23341)
        // date: randomDate(new Date(2012, 0, 1), new Date()),
      },
      {
        done: false,
        content: "Create wireframe",
        date: format_time(15340)
        // date: randomDate(new Date(2012, 0, 1), new Date()),
      },
      {
        done: false,
        content: "Organise a handover with the developers",
        date: format_time(12365)
        // date: randomDate(new Date(2012, 0, 1), new Date()),
      },
      {
        done: false,
        content: "Create workshop",
        date: format_time(32345)
        // date: randomDate(new Date(2012, 0, 1), new Date()),
      },
    ];
    const Recent = [
      {
        done: false,
        content: "Book client kick off meeting",
      },
      {
        done: false,
        content: "Send tender",
      },
    ];
    const todosData = JSON.parse(localStorage.getItem("todos")) || TodoList;
    const todos = ref(todosData);
    const completedData = JSON.parse(localStorage.getItem("recents")) || Recent;
    const recents = ref(completedData);
    function addTodo() {
      if (newTodo.value) {
        todos.value.push({
          done: false,
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

    function format_time(s) {
      const dtFormat = new Intl.DateTimeFormat("en-GB", {
        timeStyle: "medium",
        timeZone: "UTC",
      });

      return dtFormat.format(new Date(s * 1e3));
    }

    function removeTodo(index) {
      todos.value.splice(index, 1);
      saveData();
    }

    function saveData() {
      const storageData = JSON.stringify(todos.value);
      localStorage.setItem("todos", storageData);
    }

    return {
      todos,
      recents,
      newTodo,
      addTodo,
      doneTodo,
      removeTodo,
      saveData,
    };
  },
};
</script>

<style lang="scss">
@import "./assets/styles/scss/main.scss";
</style>
