<template>
  <div class="app">
    <!-- Judul Aplikasi -->
    <h1 class="title">Daftar Tugas Saya</h1>
    
    <!-- Input untuk Menambah Tugas Baru -->
    <div class="input-container">
      <input type="text" v-model="newTodo" placeholder="Tambahkan tugas baru..." @keyup.enter="addTodo">
      <button @click="addTodo" :disabled="newTodo.trim() === ''">Tambah</button>
    </div>
    
    <!-- Opsi Filter -->
    <div class="options">
      <label for="hideCompleted">
        <input type="checkbox" id="hideCompleted" v-model="hideCompleted">
        Sembunyikan yang Selesai
      </label>
      <select v-model="filterTime">
        <option value="">Semua Waktu</option>
        <option value="today">Hari Ini</option>
        <option value="week">Minggu Ini</option>
        <option value="month">Bulan Ini</option>
      </select>
    </div>
    
    <!-- Tabel Daftar Tugas -->
    <table class="todo-table">
      <thead>
        <tr>
          <th>ID</th>
          <th>Tugas</th>
          <th>Status</th>
          <th>Dibuat Pada</th>
          <th>Tindakan</th>
        </tr>
      </thead>
      <tbody>
        <!-- Menampilkan daftar tugas -->
        <tr v-for="todo in filteredTodos" :key="todo.id" :class="{ done: todo.done }">
          <td>{{ todo.id }}</td>
          <td>{{ todo.text }}</td>
          <td>{{ todo.done ? 'Selesai' : 'Belum Selesai' }}</td>
          <td>{{ formatDate(todo.timestamp) }}</td>
          <td>
            <!-- Tombol untuk Menghapus Tugas -->
            <button @click="removeTodo(todo)">Hapus</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script setup>
import { ref, computed } from 'vue';

let id = 3; // Menyimpan ID untuk tugas
const newTodo = ref(''); // Menyimpan teks tugas baru
const hideCompleted = ref(false); // Menyimpan status untuk menyembunyikan yang selesai
const filterTime = ref(''); // Menyimpan opsi waktu untuk penyaringan

const todos = ref([ // Daftar tugas awal
  { id: 1, text: 'Pratikum PBK', done: true, timestamp: Date.now() },
  { id: 2, text: 'Pratikum Grafkom', done: true, timestamp: Date.now() },
  { id: 3, text: 'Pratikum Jaringan komputer', done: false, timestamp: Date.now() }
]);

const filteredTodos = computed(() => {
  let filtered = todos.value;
  if (hideCompleted.value) {
    filtered = filtered.filter(todo => !todo.done);
  }
  if (filterTime.value === 'today') {
    filtered = filtered.filter(todo => isToday(new Date(todo.timestamp)));
  } else if (filterTime.value === 'week') {
    filtered = filtered.filter(todo => isThisWeek(new Date(todo.timestamp)));
  } else if (filterTime.value === 'month') {
    filtered = filtered.filter(todo => isThisMonth(new Date(todo.timestamp)));
  }
  return filtered;
});

// Menambahkan tugas baru
function addTodo() {
  if (newTodo.value.trim() !== '') {
    todos.value.push({ id: ++id, text: newTodo.value, done: false, timestamp: Date.now() });
    newTodo.value = '';
  }
}

// Menghapus tugas
function removeTodo(todo) {
  todos.value = todos.value.filter(t => t.id !== todo.id);
}

// Memeriksa apakah suatu tanggal adalah hari ini
function isToday(someDate) {
  const today = new Date();
  return someDate.getDate() === today.getDate() &&
    someDate.getMonth() === today.getMonth() &&
    someDate.getFullYear() === today.getFullYear();
}

// Memeriksa apakah suatu tanggal adalah dalam minggu ini
function isThisWeek(someDate) {
  const today = new Date();
  const firstDayOfWeek = new Date(today.setDate(today.getDate() - today.getDay()));
  const lastDayOfWeek = new Date(today.setDate(today.getDate() - today.getDay() + 6));
  return someDate >= firstDayOfWeek && someDate <= lastDayOfWeek;
}

// Memeriksa apakah suatu tanggal adalah dalam bulan ini
function isThisMonth(someDate) {
  const today = new Date();
  return someDate.getMonth() === today.getMonth() && someDate.getFullYear() === today.getFullYear();
}

// Format tanggal menjadi format yang terbaca
function formatDate(timestamp) {
  const date = new Date(timestamp);
  return `${date.getDate()}/${date.getMonth() + 1}/${date.getFullYear()} ${date.getHours()}:${date.getMinutes()}`;
}
</script>
<style>
/* Gaya sisa dari aplikasi */
.todo-table {
  width: 100%;
  border-collapse: collapse;
}

.todo-table th,
.todo-table td {
  padding: 10px;
  border: 1px solid #000000;
  text-align: left;
}

.todo-table th {
  background-color: #43cd3b;
}

.todo-table tbody tr:nth-child(even) {
  background-color: #439b36;
}

.todo-table tbody tr:hover {
  background-color: #d3d3d3;
}

.done {
  text-decoration: line-through;
}
</style>
