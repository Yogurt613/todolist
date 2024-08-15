<template>
  <div class="card text-center">
    <div class="input-group mb-3">
      <input v-model="newTodo" type="text" class="form-control" placeholder="準備要做的任務">
      <button @click="addTodo" class="btn btn-primary" type="button">
        新增
      </button>
    </div>

    <div class="card-header">
      <ul class="nav nav-tabs card-header-tabs">
        <li class="nav-item">
          <a class="nav-link" :class="{ active: currentStatus === 'all' }" @click="changeStatus('all')" href="#">
            所有
          </a>
        </li>
        <li class="nav-item">
          <a class="nav-link" :class="{ active: currentStatus === 'pending' }" @click="changeStatus('pending')"
            href="#">
            未完成
          </a>
        </li>
        <li class="nav-item">
          <a class="nav-link" :class="{ active: currentStatus === 'completed' }" @click="changeStatus('completed')"
            href="#">
            已完成
          </a>
        </li>
      </ul>
    </div>
    <div class="card-body">
      <ul class="list-group mt-3">
        <li v-for="todo in filteredTodos" :key="todo.id"
          class="list-group-item d-flex justify-content-between align-items-center">
          <div class="form-check">
            <input type="checkbox" v-model="todo.completed" @change="updateStatus(todo)" class="form-check-input me-2"
              :disabled="todo.status === '已完成'" />
            <span :class="{ 'text-decoration-line-through': todo.completed }">{{ todo.text }}</span>
          </div>
          <button @click="removeTodo(todo.id)" class="btn btn-danger btn-sm">刪除</button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed } from 'vue';

// 创建响应式数据
const todos = reactive([
  { id: 1, text: '代辦事項1', status: '未完成', completed: false },
  { id: 2, text: '代辦事項2', status: '未完成', completed: false },
  { id: 3, text: '代辦事項3', status: '未完成', completed: false }
]);



// 当前状态
const currentStatus = ref('all');

// 计算属性：过滤待办事项
const filteredTodos = computed(() => {
  if (currentStatus.value === 'all') {
    return todos;
  }

});

// 切换状态
function changeStatus(status) {
  currentStatus.value = status;
}

// 删除待办事项
function removeTodo(id) {
  const index = todos.findIndex(todo => todo.id === id);
  if (index !== -1) {
    todos.splice(index, 1);
  }
}

// 新增值進去
const newTodo = ref('');

// 新增
function addTodo() {
  const trimmedText = newTodo.value.trim();
  if (trimmedText) {
    const newId = todos.length + 1;
    todos.push({
      id: newId,
      text: trimmedText,
      status: '未完成',
      completed: false
    });
    newTodo.value = "";
  }
}
</script>

<style scoped>
.nav-link.active {
  font-weight: bold;
}

.text-decoration-line-through {
  text-decoration: line-through;


}
</style>
