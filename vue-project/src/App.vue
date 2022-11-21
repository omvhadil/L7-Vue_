<script setup>
import { reactive, ref, watchEffect } from "vue";

const nama = ref("Fadil 🙅");
const page = ref("page1");
const biodata = reactive({
  umur: "22 Thn",
  hobi: "Volly",
});

setTimeout(() => {
  // Ketika sudah 2 detik jalanin ini
  nama.value = "TODO LIST 🦹‍♂️";
  biodata.hobi = "Nyicipi makanan";
}, 1500);

const todo = reactive({
  data: JSON.parse(localStorage.getItem("todo") || "[]"),
  form: null,
  selected: null,
  mode: "add",
  search: null,
  page: "page1",
});

function createTodo() {
  if (todo.mode === "add") {
    if (todo.form.trim()) {
      todo.data.unshift({
        id: Math.random(),
        title: todo.form,
        completed: false,
      });
      todo.form = null;
    }
  } else {
    if (validateTodo()) {
      //mencari index dari todos.data yg akan di edit
      const index = todo.data.findIndex((e) => e.id === todo.selected.id);
      todo.data[index].title = todo.form;
      todo.form = null;
      todo.selected = null;
      todo.mode = "add";
    }
  }
};

function delTodo(idx) {
 todo.data.splice(idx, 1);
};

function cheklist(id){
  if (todo[id].completed){
    todo[id].completed=false;
  }else{
    todo[id].completed=true
  }
};

watchEffect(() => {
  localStorage.setItem("todo", JSON.stringify(todo.data))
});

</script>
<template>
  <div class="container">
  <h1>{{ nama }}</h1>
  <p>Umur : {{ biodata.umur }}</p>
  <p>Hoby: {{ biodata.hobi }}</p>
  <hr />

  <!-- ================ Button ================ -->
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
  <hr>

  <!-- ============ Create Todo ================ -->
  <form @submit.prevent="createTodo">
    <input style="width: 230px; margin-right: 10px; padding: 10px" 
          type="text" v-model="todo.form" 
          placeholder="Todo..." required>
    <button style="padding: 10px;" type="submit">💾simpan</button>
  </form>
  
  <hr>
  <!-- =================== All Todos ===================== -->
  <div v-if="page === 'page1'" class="wrapper">
    <h2>All Todos</h2>
    <ul v-for="(item, index) in todo.data" :key="item.id">
      <li style="display: flex; justify-content: space-between; border-bottom: 1px solid" @click="checkList(index)" :class="item.completed ? `completed` : ``">
        {{ item.title }}
        <div>
          <button style="margin-right: 10px;" @click="editTodo(index)">✍</button>
          <button @click="delTodo(index)">❌</button>
        </div>
      </li>
    </ul>
  </div>

  <!-- ==================== Completed todos ===================== -->
  <div v-if="page === 'page2'" class="wrapper">
    <h2>completed todos</h2>
  </div>

  <div v-if="page === 'page3'" class="wrapper">
    <h2>uncompleted todos</h2>
  </div>


  <!-- <input type="number" @input="(event) => (bil1 = event.target.value)" /> +
  <input type="number" v-model="bil2" />
  <button @click="hitung">Hasil</button>
  <br />
  <p>Hasil : {{ hasil }}</p> -->
</div>
</template>
<style>

body{
  height: 100vh;
  margin-top: 0;
  display: flex;
  justify-content: center;
}
.container{
  max-width: 400px;
  border: 1px solid;
  padding: 1rem;
  margin-top: 1.5rem;
}
.active {
  background-color: aqua;
}
li{
  list-style: none;
}
.btn{
  display: flex;
  justify-content: space-between;
}
.btn button{
  padding: .5rem;
}

</style>