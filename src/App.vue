<template>
  <header>
    <h1>Todo App</h1>
    <form @submit.prevent="addTodo">
      <label for="todoBar">
        <input id="todoBar" v-model="newTodo" placeholder="Type here..." required />
      </label>
      <button id="addTodo">+</button>
    </form>
  </header>

  <section id="todo-list-container">
    <ul id="todo-list">
      <li v-for="todo in filteredTodos" :key="todo.id">
        <label>
          <input type="checkbox" v-model="todo.done"/>
        </label>
        <p :class="{ done: todo.done }">{{ todo.text }}</p>
        <button id="removeTodo" @click="removeTodo(todo)">X</button>
      </li>
    </ul>
  </section>

  <section id="menu">
    <div class="button-container">
      <button id="hideTodo" class="button-centered" @click="hideCompleted = !hideCompleted; hideDelete(); switchEyeImage(); selectedButton()">{{ hideCompleted ? 'Show all' : 'Hide completed' }}</button>

      <button id="deleteAllTodos" class="" @click="deleteAll(todos); hideDeleteButton = !hideDeleteButton">{{ 'Delete All' }}</button>

      <button id="deleteSelectedTodos" class="" @click="deleteSelected()">{{ 'Delete Selected' }}</button>
    </div>
  </section>
</template>

<script>
let id = 0

export default {
  name: 'App',
  components: {
    // Draggable
  },
  props: {
    msg: String
},

  data() {
    return {
      drag: false,
      newTodo: '',
      hideCompleted: false,
      hideDeleteButton: false,
      todos: [
        { id: id++, text: 'Que vous reste-t-il donc à faire aujourd\'hui ?', done: false },
        { id: id++, text: 'Vous pouvez supprimer une tâche en appuyant sur la croix !', done: false },
        { id: id++, text: 'Ou alors vous pouvez la sélectionner/barrer en cliquant sur la case...', done: true }
      ],
    }
  },

  computed: {
    filteredTodos() {
      return this.hideCompleted ? this.todos.filter((t) => !t.done) : this.todos;
    }
  },

  methods: {
    addTodo() {
      this.todos.push({ id: id++, text: this.newTodo, done: false });
      this.newTodo = '';
    },
    removeTodo(todo) {
      this.todos = this.todos.filter((t) => t !== todo);
    },
    deleteAll() {
      this.todos.splice(0, this.todos.length);
    },
    hideDelete() {
      document.getElementById('deleteAllTodos').toggleAttribute('hidden');
      document.getElementById('deleteSelectedTodos').toggleAttribute('hidden');
    },
    deleteSelected() {
      let toBeRemoved = [];
      for (let n of this.todos) {
        if (n.done === true) {
          toBeRemoved.push(this.todos.indexOf(n));
        }
      }
      for (let index = toBeRemoved.length -1; index >= 0; index--) {
        this.todos.splice(toBeRemoved[index], 1);
      }
    },
    switchEyeImage() {
      this.currentEyeSrc += 1;
      if (this.currentEyeSrc > 1) {
        this.currentEyeSrc = 0;
      }
    },
    selectedButton() {
      if (!document.getElementById('hideTodo').classList.contains('selectedButton')) {
        document.getElementById('hideTodo').classList.add('selectedButton');
      } else {
        document.getElementById('hideTodo').classList.remove('selectedButton');
      }
    }
  },

  mounted() {
    let vm = this;
    let targetNode = document.getElementById('todo-list');
    let config = { childList: true };

    function disableButtons() {
      if (vm.todos.length === 0) {
        document.getElementById('hideTodo').setAttribute('disabled', '');
        document.getElementById('deleteAllTodos').setAttribute('disabled', '');
        document.getElementById('deleteSelectedTodos').setAttribute('disabled', '');
      } else {
        document.getElementById('hideTodo').removeAttribute('disabled');
        document.getElementById('deleteAllTodos').removeAttribute('disabled');
        document.getElementById('deleteSelectedTodos').removeAttribute('disabled');
      }
    }

    let observer = new MutationObserver(disableButtons);
    observer.observe(targetNode, config);

    document.getElementById('todoBar').addEventListener('input', () => {
      if (document.getElementById('todoBar').value.length > 0) {
        document.querySelector('label').style.setProperty('--vermilion', 'lightgreen');
      } else {
        document.querySelector('label').style.setProperty('--vermilion', '#E3170A');
      }
    })
  }
}
</script>
