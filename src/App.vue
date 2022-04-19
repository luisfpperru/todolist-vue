<template>
  <main id="app">
    <section class="todo-list">
      <h3>TODOS List</h3>
      <div class="all-todos">
        <div
          v-for="(todo, index) in todos"
          :key="index"
          class="single-todo"
          :class="{ done: todo.done }"
          @click="
            todo.done = !todo.done;
            editTodo(todo);
          "
        >
          <p>{{ todo.name }}</p>
          <div class="remove-button" @click="deleteTodo(todo.id)">❌</div>
        </div>
      </div>
      <input type="text" placeholder="Write a new TODO..." v-model="newText" />
      <button class="add" @click="addTodo()">Add</button>

      <div class="instructions">
        Instructions:
        <ul>
          <li>Right click a TODO to change done/ undone</li>
          <li>Use the X to remove the TODO from the list</li>
          <li>Use the input to add new TODOS</li>
        </ul>
      </div>
    </section>
  </main>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  components: {},
  data() {
    return {
      todos: [],
      newText: "",
    };
  },
  methods: {
    addTodo: function () {
      if (this.newText) {
        axios
          .post("http://localhost:3000/todos", {
            name: this.newText,
          })
          .then((response) => {
            this.todos.push(response.data);
            this.newText = "";
          });
      } else {
        alert("TODO name cannot be empty");
      }
    },
    editTodo(todoEdited) {
      axios
        .put(`http://localhost:3000/todos/${todoEdited.id}`, todoEdited)
        .then((response) => {
          let newTodo = this.tarefas.filter(
            (todo) => todo.id === todoEdited.id
          );
          newTodo.name = response.data.name;
          newTodo.done = response.data.done;
        });
    },
    deleteTodo(id) {
      axios.delete(`http://localhost:3000/todos/${id}`).then(() => {
        this.todos = this.todos.filter((todo) => todo.id != id);
      });
    },
  },
  created() {
    axios.get("http://localhost:3000/todos").then((response) => {
      let todoslist = response.data;
      todoslist.forEach((todo) => this.todos.push(todo));
    });
  },
};
</script>

<style>
body {
  margin: 0;
  font-family: "Open Sans", sans-serif;
}

main {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  flex-wrap: wrap;
  padding-top: 60px;
}

main .todo-list input {
  box-sizing: border-box;
  height: 28px;
  border-radius: 0.25rem;
  width: 60%;
  border: 1px solid lightgrey;
  margin-top: 32px;
}

main button {
  background-color: transparent;
  cursor: pointer;
  box-sizing: border-box;
  height: 28px;
  border-radius: 0.25rem;
  color: #fff;
}

main button:hover {
  opacity: 0.8;
}

main button.add {
  background-color: #007bff;
  border: 1px solid #007bff;
  margin-left: 2px;
}

main button.clear {
  background-color: #dc3545;
  border: 1px solid #dc3545;
  display: block;
  margin: auto;
}

main button:focus {
  outline: 0;
}

main > section.todo-list h3 {
  text-align: center;
  margin-top: 24px;
  width: 100%;
}

main > section.todo-list {
  flex-wrap: wrap;
  border: 1px solid lightgrey;
  background-color: rgb(248, 248, 248);
  padding: 20px;
  max-width: 500px;
  min-width: 300px;
  text-align: center;
  border-radius: 0.25rem;
}

main .all-todos .single-todo {
  display: flex;
  align-items: center;
  justify-content: center;
}

main .all-todos .single-todo p {
  margin: 12px 0;
  cursor: pointer;
}

main .all-todos .single-todo.done p {
  text-decoration: line-through;
}

main .all-todos .single-todo button.remove {
  width: 12px;
  height: 12px;
  border: none;
  background-size: contain;
  cursor: pointer;
  margin-left: 8px;
}

main > section.todo-list button.clear {
  margin-top: 24px;
}

div.instructions {
  font-size: 11px;
  margin-top: 40px;
  color: grey;
}

div.instructions ul {
  list-style: inside;
  text-align: center;
  padding: 0;
}

div.instructions ul li {
  margin: 4px auto;
}
.remove-button {
  cursor: pointer;
}
</style>