<script setup>
import { ref } from 'vue';
import { statuses } from '../const/statuses';

const todo = ref("");
const todoDate = ref("");

const isError = ref(false);
// 登録ボタンクリックで実行される関数
function onSubmitForm(){
  if(todo.value == "" || todoDate.value == ""){
    isError.value = true;
    event.preventDefault();
    return;
  }
  // ローカルストレージからデータを取得、配列で扱えるように変換
  // "items"がkeyにあたる
  const items = JSON.parse(localStorage.getItem("items"))||[];

  // 追加する内容のオブジェクトを作成
  const newItem = {
    id: items.length,
    content: todo.value,
    limit: todoDate.value,
    state: statuses.NOT_START,
    onEdit: false,
  };

  // 作成したオブジェクトnewItemsをitemsに追加する
  items.push(newItem);
  // 配列をローカルストレージに保存
  localStorage.setItem("items",JSON.stringify(items));
}

</script>

<template>
  <div class="todo">
    <p v-if="isError">タスク・期限の両方を入力してください。</p>
    <form @submit="onSubmitForm" class="form">
        <label class="item">Todo</label>
        <input type="text" v-model="todo" class="input-todo"/>
        <label class="item">期限</label>
        <input type="date" v-model="todoDate" class="input-todo"/>
      <input type="submit" value="登録" class="registration"/>
    </form>
  </div>
</template>
