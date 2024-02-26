<script setup>
import { statuses } from "@/const/statuses";
import { ref } from "vue";

//refを使用して、画面の変更を反映させる
let items = ref(JSON.parse(localStorage.getItem("items")) || []);

let todoContent = ref();
let todoLimit = ref();
let todoState = ref();

function onEdit(id) {
  let isOnEdit = false;
  items.value.map((item) => {
    if (item.onEdit) {
      isOnEdit = true;
      return;
    }
  });
  if (isOnEdit) {
    errMsg.value = "他の編集中のTaskがあります！";
    isError.value = true;

    return;
  }
  todoContent.value = items.value[id].content;
  todoLimit.value = items.value[id].limit;
  todoState.value = items.value[id].state;
  items.value[id].onEdit = true;
}

let isError = ref(false);
let errMsg = ref("");

function onUpdated(id) {
  if (todoContent.value == "" || todoLimit.value == "") {
    isError.value = true;
    return;
  }
  const newItem = {
    id: id,
    content: todoContent.value,
    limit: todoLimit.value,
    state: todoState.value,
    onEdit: false,
  };

  items.value.splice(id, 1, newItem);
  localStorage.setItem("items", JSON.stringify(items.value));
  // 更新が完了したらisErrorはfalseになるように設定
  isError.value = false;
}

let isShowModal = ref(false);

function showDeleteModal(id) {
  isShowModal.value = true;
  deleteItemId = id;
  deleteItemContent = items.value[id].content;
}

function onDeleteItem() {
  items.value.splice(deleteItemId, 1);
  isShowModal.value = false;
}

function onReturn(id) {
  isShowModal.value = false;
  deleteItemId = id;
}

// 削除対象のアイテムのID
let deleteItemId = "";
let deleteItemContent = ref();

// IDだけを配列のindexに合わせる
// 各要素ごとに関数を適用させて、新しいオブジェクトを作成する
items.value = items.value.map((item, index) => ({
  id: index,
  content: item.content,
  limit: item.limit,
  state: item.state,
  onEdit: item.onEdit,
}));
localStorage.setItem("items", JSON.stringify(items.value));

const today = new Date();

function sortByLimit(){
  // items.value.sort メソッドでitems配列をソート
  //2つのオブジェクトを比較して、日付が過去のものから並び替えられるように設定
  items.value.sort((a,b) => new Date(a.limit) - new Date(b.limit));
  localStorage.setItem("items",JSON.stringify(items.value));
}

function sortById(){
  items.value.sort((a,b) => a.id - b.id);
  localStorage.setItem("items",JSON.stringify(items.value));
}

</script>

<template>
  <div class="todo">
    <!-- テーブル部分 -->
    <table>
      <!-- テーブルヘッダー -->
      <tr>
        <th class="th-id" colspan="2">ID<button @click="sortById()" class="sort-button">👇</button></th>
        <th class="th-content">Todo</th>
        <th class="th-limit" colspan="2">期日<button @click="sortByLimit()" class="sort-button">👇</button></th>
        <th class="th-state">Status</th>
        <th class="th-edit">Edit</th>
        <th class="th-delete">Delete</th>
      </tr>

      <!-- エラーメッセージ -->
      <p v-if="isError" class="error">{{ errMsg }}</p>

      <!-- テーブルデータ -->
      <tr v-for="item in items" :key="item.id" :class="{red: new Date(item.limit) < today}">
        <!-- 各列のデータ -->
        <td class="td-id" colspan="2">{{ item.id }}</td>
        <td class="td-content">
          <span v-if="!item.onEdit">{{ item.content }}</span>
          <input v-else type="text" v-model="todoContent" />
        </td>
        <td class="td-limit" colspan="2">
          <span v-if="!item.onEdit">{{ item.limit }}</span>
          <input v-else type="date" v-model="todoLimit" />
        </td>
        <td class="td-state">
          <span v-if="!item.onEdit">{{ item.state.value }}</span>
          <select v-else v-model="todoState">
            <option
              v-for="state in statuses"
              :key="state.id"
              :value="state"
              :selected="state.id == item.state.id"
            >
              {{ state.value }}
            </option>
          </select>
        </td>
        <td class="td-edit">
          <button v-if="!item.onEdit" @click="onEdit(item.id)" class="task-button">Edit</button>
          <button v-else @click="onUpdated(item.id)" class="task-button">Done</button>
        </td>
        <td class="td-delete"><button @click="showDeleteModal(item.id)" class="task-button">Delete</button></td>
      </tr>
    </table>

    <!-- モーダル部分 -->
    <div class="modal" v-if="isShowModal">
      <div class="modal-content">
        <p>本当に【{{ deleteItemContent }}】削除してもよろしいでしょうか？</p>
        <button @click="onDeleteItem()" class="modal-button">はい</button>
        <button @click="onReturn()">キャンセル</button>
      </div>
    </div>
  </div>
</template>
