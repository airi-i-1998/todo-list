<script setup>
const items = JSON.parse(localStorage.getItem("items")) || [];
</script>

<template>
  <id>
    <tr>
      <th class="th-id">ID</th>
      <th class="th-content">Todo</th>
      <th class="th-limit">期日</th>
      <th class="th-state">Status</th>
      <th class="th-edit">Edit</th>
      <th class="th-delete">Delete</th>
    </tr>
    <tr v-for="item in items" :key="item.id">
      <td>{{ item.id }}</td>
      <td>{{ item.content }}</td>
      <td>{{ item.limit }}</td>
      <td>{{ item.state.value }}</td>
      <td><button>Edit</button></td>
      <td><button>Delete</button></td>
      <td></td>
    </tr>
  </id>

</template>
