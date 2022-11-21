<script setup>
import { computed, reactive, watchEffect } from "vue";

/*  */
const todo = reactive({
  data: JSON.parse(localStorage.getItem("todos") || "[]"),
  namaBuku: null,
  penerbitBuku: null,
  hargaBuku: null,
  //namaBuku, penerbitBuku, hargaBuku berfungsi untuk menangkap hasil model
  selected: null,
  mode: "add",
  search: null,
  page: "page1",
});

// ========= Function untuk menambahkan data ==================
function createTodo() {
  if (todo.mode === "add") {
    if (todo.namaBuku, todo.penerbitBuku, todo.hargaBuku.trim()) {
      todo.data.unshift({
        id: Math.random(),
        namaBuku: todo.namaBuku,
        penerbitBuku: todo.penerbitBuku,
        hargaBuku: todo.hargaBuku,
        completed: false,
      });
      todo.namaBuku = null; todo.penerbitBuku = null; todo.hargaBuku = null;
    }
  } 
  
  else {
    if (todo.namaBuku, todo.penerbitBuku, todo.hargaBuku.trim()) {
      // Carikan index dari todo.data yang akan diedit
      const i = todo.data.findIndex((o) => o.id === todo.selected.id);
      // Ubah data pada index tersebut
      todo.data[i].namaBuku = todo.namaBuku;
      todo.data[i].penerbitBuku = todo.penerbitBuku;
      todo.data[i].hargaBuku = todo.hargaBuku;
      // Reset
      todo.selected = {};
      todo.form = null;
      todo.mode = "add";
    }
  }
};

// ============= Arrow function untuk me-return ulang data ===============
const filteredTodos = computed(() => {
  return todo.data;
});

// ============== Function untuk menghapus todolist yang dipilih=========== 
function delTodo(idx) {
    todo.data.splice(idx, 1);
};


// =============== Arrow function untuk mengirim data ke localStorage =========
watchEffect(() => {
  localStorage.setItem("todos", JSON.stringify(todo.data));
  console.log(todo.data);
});



</script>

<template>
  <div class="container">
    <!-- ========== Header =========== -->
    <header>
      <h2>APLIKASI CRUD PERPUSTAKAAN</h2>
      <p>Dibuat oleh: Fadilatur Rohman</p>
    </header>
    <br><hr><br>


    <section class="content">
      <!-- ================ Form Input ============ -->
      <form class="input" @submit.prevent="createTodo">
        <p>Judul Buku</p>
        <input type="text" v-model="todo.namaBuku">
        <!-- v-model untuk memberikan nama model -->

        <p>Penerbit Buku</p>
        <input type="text" v-model="todo.penerbitBuku">

        <p>Harga Buku</p>
        <input type="text" v-model="todo.hargaBuku">

        <button>{{ todo.mode === "add" ? "Simpan" : "Edit" }}</button>
      </form>
      <br><hr><br>

      <!-- =========== Tabel List item ================ -->
      <th>
        <td>Judul Buku</td><td>Penerbit Buku</td><td>Jenis Buku</td><td>Aksi</td>
      </th>
      <tabel v-for="(item, index) in filteredTodos" :key="item.id">
          <tr>
            <td :class="item.completed ? 'completed' : ''">{{ item.namaBuku }}</td>
            <td :class="item.completed ? 'completed' : ''">{{ item.penerbitBuku }}</td>
            <td :class="item.completed ? 'completed' : ''">{{ item.hargaBuku }}</td>
            <td>
              <button>edit</button>
              <button @click="delTodo(index)">Hapus</button>
            </td>
          </tr>
      </tabel>
    </section>
  </div>
  
</template>

<style>
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body{
  background-color: aquamarine;
}
.container {
  max-width: 500px;
  margin: 0 auto;
  font-size: 1rem;
  background: rgb(236, 236, 236);
  padding: 1.5rem;
  border: 1px solid;
  margin-top: 2rem;
  border-radius: 20px;
}
.input input{
  margin: .2rem 0 .5rem;
  padding: .3rem;
  outline: none;
  width: 100%;
}
.input button{
  padding: .4rem 2rem;
  margin-top: .5rem;
  background-color: rgb(26, 128, 217);
  border: none;
  color: #fff;
  cursor: pointer;
}
th, tr{
  width: 100%;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  justify-items: center;
  background-color: rgb(26, 128, 217);
  padding: .3rem;
  color: #fff;
  font-size: .9rem;
}
tr{
  margin-top: .5rem;
  background-color: rgb(236, 236, 236);
  justify-content: space-between;
  color: #000;
}
td button{
  padding: .2rem .4rem;
  background-color: red;
  border: none;
  color: #fff;
  cursor: pointer;

}
td button:nth-child(1){
  margin-right: .4rem;
  background-color: green;
}
</style>
