<style lang="scss">
@import "./assets/styles/scss/main.scss";
</style>
<template>
  <header>
    <div class="navbar shadow-sm">
      <div class="container d-flex justify-content-between">
        <a href="#" class="navbar-brand d-flex align-items-center">
          <h5>Click-Bait Industries</h5>
        </a>
        <img
          class="avatar"
          src="https://i.pravatar.cc/50"
          alt="random-avatar"
        />
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
          @click="toggle(item, $event)"
        >
          <label class="wrapper">
            <input type="checkbox" />
            <span class="checkmark"></span>
          </label>
          <div>
            <span>{{ item.content }}</span>
            <small>{{ item.days }} days, {{ item.hours }} hours, {{ item.minutes }} minutes, {{ counter }} seconds</small>
          </div>
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
          <div>
            <span>{{ item.content }}</span>
            <small>Completed {{ item.days }} days ago</small>
          </div>
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
  mounted: function() {
      this.$nextTick(() => {
          this.timer();
      });
  },
  created: function () {
    document.body.style.backgroundColor = "#27292d";
    document.body.style.color = "#fff";
  },
  unmounted: function () {
    document.body.style.backgroundColor = null;
    document.body.style.color = null;
  },

  computed: {
    done() {
      /* Filter by done & and sorted alphabetically */
      return this.todos
        .filter((i) => i.done)
        .sort((a, b) => {
          if (a.content < b.content) return -1;
          if (a.content > b.content) return 1;
          return 0;
        });
    },

    undone() {
      return this.todos.filter((i) => !i.done);
    },
  },

  methods: {
    toggle(todo) {
      todo.done = !todo.done;
      if (!todo.done) {
        this.timerToRemoveTodo(todo);
        this.saveData();
      }
    },
    timer: function() { setInterval(this.ticker, 1000); },
    ticker: function() { 
       /* Update counters */
      ++this.counter; 
      if (this.counter == 60) {
          this.counter = 0;
          this.todos.map(todo => {
            todo.minutes++;
            if (todo.minutes == 60) {
              todo.minutes = 0;
              this.todos.map(todo => {
                todo.hours++;
              });
              if (todo.hours == 24) {
                  todo.hours = 0;
                  todo.days++;
              } 
            }
            return;
          });
        }
      }
  },
  data() {
    return {
      text: "",
      placeholder: "Enter your task",
      completedCounter: 0,
      counter: 0
    };
  },
  watch: {
    completedCounter: {
      handler(value) {
        if (value < 120) {
          setTimeout(() => {
            this.completedCounter++;
            console.log(this.completedCounter);
          }, 1000);
        } else {
          alert("Todo completed will be removed!");
        }
      },
    },
  },
  setup() {
    const newTodo = ref("");
    const TodoList = [
      { id: 0, content: "Create a report", done: true, days: 23, hours: 2, minutes: 19, seconds: 0 },
      { id: 1, content: "Create UI", done: true, days: 12, hours: 9, minutes: 0, seconds: 0 },
      { id: 2, content: "Create a user and wire flow", done: true, days: 0, hours: 17, minutes: 45, seconds: 0 },
      { id: 3, content: "Create wireframes", done: true, days: 1, hours: 2, minutes: 13, seconds: 0 },
      { id: 4, content: "Organise a handover with the developers", done: true, days: 15, hours: 8, minutes: 32, seconds: 0 },
      { id: 5, content: "Create workshop", done: true, days: 57, hours: 23, minutes: 59, seconds: 0 },
      { id: 6, content: "Book client kick off meeting", done: false, days: 80, hours: 18, minutes: 15, seconds: 0 },
      { id: 7, content: "Send tender", done: false, days: 16, hours: 9, minutes: 37, seconds: 0 }
    ];
    const todosData = JSON.parse(localStorage.getItem("todos")) || TodoList;
    const todos = ref(todosData);
    function addTodo() {
      if (newTodo.value) {
        todos.value.push({
          seconds: 0,
          minutes: 0,
          hours: 0,
          days: 0,
          id: todos.value.length,
          done: true,
          content: newTodo.value,
        });
        newTodo.value = "";
      }
      saveData();
    }

    function saveData() {
      const storageData = JSON.stringify(todos.value);
      localStorage.setItem("todos", storageData);
    }

    function timerToRemoveTodo(todo) {
      if (todo.done == false) {
        let timer = 0;
        if (timer == 0) {
            this.completedCounter = 0;
            this.completedCounter++;
            console.log("Counter starts running");
        }
      }
    }

    return {
      todos,
      newTodo,
      addTodo,
      saveData,
      timerToRemoveTodo
    };
  },
};
</script>
