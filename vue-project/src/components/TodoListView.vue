<script setup>
import { statuses } from "@/const/statuses";
import { ref } from "vue";

//refを使用して、画面の変更を反映させる
let items = ref(JSON.parse(localStorage.getItem("items")) || []);

let todoContent = ref();
let todoLimit = ref();
let todoState = ref();

function onEdit(id) {
  todoContent.value = items.value[id].content;
  todoLimit.value = items.value[id].limit;
  todoState.value = items.value[id].state;
  items.value[id].onEdit = true;
}

const isError = ref(false);

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

function showDeleteModal() {
  isShowModal.value = true;
}

function onDeleteItem() {
  items.value.splice(deleteItemId,1);
  isShowModal.value = false;
}

function onReturn(id) {
  isShowModal.value = false;
  deleteItemId = id
}

// 削除対象のアイテムのID
const deleteItemId = "";

// IDだけを配列のindexに合わせる
// 各要素ごとに関数を適用させて、新しいオブジェクトを作成する
items.value = items.value.map((item,index)=>({
  id: index,
  content: item.content,
  limit: item.limit,
  state: item.state,
  onEdit: item.onEdit,
}));
localStorage.setItem("items", JSON.stringify(items.value));

</script>

<<template>
  <div>
    <!-- テーブル部分 -->
    <table>
      <!-- テーブルヘッダー -->
      <tr>
        <th class="th-id">ID</th>
        <th class="th-content">Todo</th>
        <th class="th-limit">期日</th>
        <th class="th-state">Status</th>
        <th class="th-edit">Edit</th>
        <th class="th-delete">Delete</th>
      </tr>

      <!-- エラーメッセージ -->
      <p v-if="isError">タスク・期限の両方を入力してください。</p>

      <!-- テーブルデータ -->
      <tr v-for="item in items" :key="item.id">
        <!-- 各列のデータ -->
        <td>{{ item.id }}</td>
        <td>
          <span v-if="!item.onEdit">{{ item.content }}</span>
          <input v-else type="text" v-model="todoContent" />
        </td>
        <td>
          <span v-if="!item.onEdit">{{ item.limit }}</span>
          <input v-else type="date" v-model="todoLimit" />
        </td>
        <td>
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
        <td>
          <button v-if="!item.onEdit" @click="onEdit(item.id)">Edit</button>
          <button v-else @click="onUpdated(item.id)">Done</button>
        </td>
        <td><button @click="showDeleteModal(item.id)">Delete</button></td>
      </tr>
    </table>

    <!-- モーダル部分 -->
    <div class="modal" v-if="isShowModal">
      <div class="modal-content">
        <p>本当に削除してもよろしいでしょうか？</p>
        <button @click="onDeleteItem()">はい</button>
        <button @click="onReturn()">キャンセル</button>
      </div>
    </div>
  </div>
</template>
