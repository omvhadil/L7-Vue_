<script setup>
import { computed, reactive, ref } from "vue";

const nama = ref("TODO LIST");
const page = ref("page1");

const biodata = reactive({
  umur: "22  Thn",
  hobi: "Bola",
});

const todos = reactive({
  data: JSON .parse(localStorage.getItem("todo") || "[]"),
  form: null,
  selected: null,
  mode: "add",
  search: null,
  page: "page1",
});

function createTodo() {
  if (todos.mode === "add") {
    if (validateTodo()) {
      todos.data.unshift({
        id: Math.random(),
        title: todos.form,
        completed: false,
      });
      todos.form = null;
    }
  } else {
    if (validateTodo()) {
      const index = todos.data.findIndex((o) => o.id === todos.selected.id);

      todos.data[index].title = todos.form;
      todos.selected = {};
      todos.form = null;
      todos.mode = "add";
    }
  }
}

function validateTodo() {
  return todos.form.trim();
}

function checkList(id) {
  if (todos[id].completed) {
    todos[id].completed = false;
  } else {
    todos[id].completed = true;
  }
}
function delTodo(idx) {
  todos.splice(idx, 1);
}

const filteredTodos = computed(() => {
  if (todos.search) {
    return todos.data.filter((data) => {
      return data.title.toLowerCase().Match(todos.search.toLowerCase());
    });
  } else {
    return todos.data;
  }
});

</script>
<template>
  <div class="container">
    <h1>{{ nama }}</h1>
    <p>Umur : {{ biodata.umur }}</p>
    <p>Hoby: {{ biodata.hobi }}</p>
    <hr />

    <!-- ========== Create Todo ========= -->
  <form @submit.prevent="createTodo">
    <input type="text" placeholder="todo...." v-model="todos.search" />
    <button type="submit">
      💾 {{ todos.mode == "add" ? "simpan" : " edit" }}
    </button>
  </form>
<br>
  <form>
    <input type="search" placeholder="🔍....." />
    <button type="button">🔍Search</button>
  </form>
  <hr>

  <!-- ========= All Todo ============= -->
  <div v-if="todos.page === 'page1'" class="wrapper">
    <h2>📋All Todos</h2>
    <ul
      v-for="(item, index) in todos.filteredTodos"
      :key="item.id"
      style="display: flex"
    >
      <li @click="checkList(index)" :class="item.completed ? 'completed' : ''">
        {{ item.title }}
      </li>
      <button
        style="margin-right: 10px"
        @click="
          todos.mode = 'edit';
          todos.selected = item;
          todos.form = item.title;
        "
      >
        ✍️
      </button>
      <button @click="delTodo(index)">❌</button>
    </ul>
  </div>

  <div
    v-if="page === 'page2'"
    class="wrapper"
    style="background-color: greenyellow"
  >
    <h2>completed todos</h2>
  </div>

  <div v-if="page === 'page3'" class="wrapper">
    <h2>uncompleted todos</h2>
  </div>
  <div class="btn">
    <button @click="page = 'page1'" :class="page === 'page1' ? 'active' : ''">
      All Todos
    </button>
    <button @click="page = 'page2'" :class="page === 'page2' ? 'active' : ''">
      completed Todos
    </button>
    <button @click="page = 'page3'" :class="page === 'page3' ? 'active' : ''">
      uncompleted Todos
    </button>
  </div>
</div>
</template>
<style>
body {
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  font-family: "Times New Roman", Times, serif;
}
.container {
  max-width: 800px;
  border: 1px solid;
  padding: 1.5rem;
}
.active {
  background-color: rgb(4, 255, 0);
}
.completed {
  text-decoration: line-through;
  color: gray;
}
.input todo {
  display: flex;
}
.checkList {
  display: flex;
}
</style>
1