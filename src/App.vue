<script setup>
import { ref, onMounted, computed, watch } from "vue";
const todos = ref([]);
const name = ref("");
const input_content = ref("");
const input_category = ref(null);
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return a.createdAt - b.createdAt;
  })
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  {
    deep: true,
  }
);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime(),
  });
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up,
        <input
          type="text"
          id="name"
          placeholder="寫下您的名稱"
          v-model="name"
        />
      </h2>
    </section>

    <section class="create-todo">
      <h3>ToDo-List</h3>

      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>創建待辦事項</h4>

        <input
          type="text"
          name="content"
          id="content"
          placeholder="在這裡添加新的待辦事項.."
          v-model="input_content"
        />

        <h4>選擇類別</h4>

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>工作事項</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category2"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>個人＆生活</div>
          </label>
        </div>

        <input type="submit" value="新增" />
      </form>
    </section>

    <section class="todo-list">
      <h3>待辦事項紀錄</h3>
      <div class="list">
        <div
          v-for="todo in todos_asc"
          :key="todo.id"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span
              :class="`bubble ${
                todo.category == 'business' ? 'business' : 'personal'
              }`"
            ></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">刪除</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<style setup></style>
