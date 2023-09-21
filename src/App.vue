<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up,
        <input type="text" id="name" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          name="content"
          id="content"
          placeholder="e.g. make a video"
          v-model="input_content"
        />

        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="work"
              v-model="input_category"
            />
            <span class="bubble work"></span>
            <div>Work</div>
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
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <input
        type="text"
        id="search"
        placeholder="Search todos"
        v-model="searchQuery"
      />

      <!-- Add a button to toggle the filtering option -->
      <button @click="toggleShowCompleted" class="toggle-button">
        {{ showCompleted ? "Show All" : "Show Completed" }}
      </button>

      <div class="list" id="todo-list">
        <div v-if="filteredTodos.length === 0" class="not-found">
          No todos found.
        </div>
        <div
          v-for="todo in filteredTodos"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span
              :class="`bubble ${todo.category == 'work' ? 'work' : 'personal'}`"
            ></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const searchQuery = ref("");
const showCompleted = ref(false); // Added for filtering

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

const toggleShowCompleted = () => {
  showCompleted.value = !showCompleted.value;
};

const filteredTodos = computed(() => {
  const query = searchQuery.value.toLowerCase();
  const filtered = todos.value.filter((todo) => {
    return (
      (todo.content.toLowerCase().includes(query) ||
        todo.category.toLowerCase().includes(query)) &&
      (!showCompleted.value || todo.done)
    );
  });

  if (filtered.length === 0 && query.trim() !== "") {
    // No todos found, and the search query is not empty
    // You can add custom logic here if needed
  }

  return filtered;
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<style scoped>
/* Your CSS styles go here */
/* ... Existing CSS */
</style>


<style scoped>
/* Your CSS styles go here */
.app {
  font-family: Arial, sans-serif;
  background-color: #f0f0f0;
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.greeting {
  text-align: center;
  margin-bottom: 20px;
}

.create-todo,
.todo-list {
  background-color: #fff;
  margin-top: 20px;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 600px;
}
.toggle-button {
  background-color: #007bff; /* Blue color for button */
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 5px;
  cursor: pointer;
}

.toggle-button:hover {
  background-color: #0056b3; /* Darker blue on hover */
}

.title {
  font-size: 24px;
  margin-bottom: 20px;
}

h3 {
  font-size: 20px;
  margin-bottom: 10px;
}

h4 {
  font-size: 16px;
  margin-top: 20px;
}

#content,
#search {
  width: 100%;
  padding: 10px;
  margin: 10px 0;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.label {
  display: flex;
  align-items: center;
}

.todo-item {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  padding: 10px;
  border-radius: 5px;
}

.todo-item. {
  text-decoration: line-through;
  opacity: 0.7;
}

.todo-content input[type="text"] {
  flex: 1;
  border: none;
  border-bottom: 1px solid #ccc;
  padding: 5px;
  margin-left: 10px;
}

.actions {
  margin-left: 10px;
}

.delete {
  background-color: #ff3333;
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 5px;
  cursor: pointer;
}

.delete:hover {
  background-color: #ff0000;
}

.not-found {
  text-align: center;
  font-size: 16px;
  color: #f00; /* Red color for error message */
}
</style>
