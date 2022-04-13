<template>
  <main id="app">
    <section class="todo-list">
      <h3>Lista de Tarefas</h3>
      <div class="all-todos">
        <div
          v-for="(tarefa, index) in tarefas"
          :key="index"
          class="single-todo"
          :class="{ done: tarefa.concluido }"
          @click="tarefa.concluido = !tarefa.concluido; editarTarefa(tarefa)"
        >
          <p>{{ tarefa.texto }}</p>
          <div class="remove-button "
            @click="excluirTarefa(tarefa.id)"
          >
            ❌
          </div>
        </div>
      </div>
      <input
        type="text"
        placeholder="Escreva uma nova tarefa..."
        v-model="novoTexto"
      />
      <button class="add" @click="addTarefa()">Adicionar</button>

      <div class="instructions">
        Instruções:
        <ul>
          <li>
            Clique no texto da tarefa para alternar entre feita / não feita
          </li>
          <li>Use o X para remover uma tarefa</li>
          <li>Use o input para adicionar novas tarefas</li>
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
      tarefas: [],
      novoTexto: ""
    };
  },
  methods: {
    addTarefa: function () {
      if (this.novoTexto) {
        axios
          .post("http://localhost:3000/todos", { name: this.novoTexto })
          .then((response) => {
            let novaTarefa = {
              id: response.data.id,
              texto: response.data.name,
              concluido: response.data.done,
            };
            this.tarefas.push(novaTarefa);
            this.novoTexto = "";
          });
      } else {
        alert("Tarefa não pode ser vazia");
      }
    },
    editarTarefa(tarefa){
        axios
          .put(`http://localhost:3000/todos/${tarefa.id}`,{name: tarefa.texto,done: tarefa.concluido})
          .then((response) => {
              let tarefaEditada = this.tarefas.filter((todo) => todo.id === tarefa.id)
              tarefaEditada.texto = response.data.name;
              tarefaEditada.concluido = response.data.done;
          })
    },
    excluirTarefa(id) {
      axios
          .delete(`http://localhost:3000/todos/${id}`)
          .then(() => {
             this.tarefas = this.tarefas.filter((tarefa) => tarefa.id != id);
      });
    },
  },
  created() {
    axios.get("http://localhost:3000/todos").then((response) => {
      const todos = response.data;
      todos.forEach((todo) => {
        let novaTarefa = {
          id : todo.id,
          texto: todo.name,
          concluido: todo.done,
        };
        this.tarefas.push(novaTarefa);
      });
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
.remove-button{
  cursor: pointer;
}
</style>