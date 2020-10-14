<template>
  <div class="parent">
    <div>
      <input
        type="text"
        class="todo-input"
        placeholder="What needs to be done?"
        v-model="newTodo"
        @keyup.enter="addTodo"
      />
      <transition-group
        name="fade"
        enter-active-class="animated fadeInUp"
        leave-active-class="animated fadeOutDown"
      >
        <div
          v-for="(todo, index) in todosFiltered"
          :key="todo.id"
          class="todo-item"
        >
          <div class="todo-item-left">
            <input type="checkbox" v-model="todo.completed" />
            <div
              v-if="!todo.editing"
              @dblclick="editTodo(todo)"
              class="todo-item-label"
              v-bind:class="{ completed: todo.completed }"
            >
              {{ todo.title }}
            </div>
            <input
              v-else
              class="todo-item-edit"
              type="text"
              v-model="todo.title"
              @blur="doneEdit(todo)"
              @keyup.enter="doneEdit(todo)"
              @keyup.esc="cancelEdit(todo)"
              v-focus
            />
          </div>
          <div class="remove-item" @click="removeTodo(index)">&times;</div>
        </div>
      </transition-group>
    </div>

    <div>
      <div>
        <label class="extra-container">
          <input
            type="checkbox"
            :checked="!anyRemaining"
            @change="checkAllTodos"
          />
          <p>Check All</p>

          <div class="remaining">{{ remaining }} items left</div>
        </label>
      </div>
    </div>

    <div class="extra-container">
      <div>
        <button :class="{ active: filter == 'all' }" @click="filter = 'all'">
          All
        </button>
        <button
          :class="{ active: filter == 'active' }"
          @click="filter = 'active'"
        >
          Active
        </button>
        <button
          :class="{ active: filter == 'completed' }"
          @click="filter = 'completed'"
        >
          completed
        </button>
      </div>
    </div>

    <div>
      <transition name="fade">
        <button v-if="showClearedCompletedButton" @click="clearCompleted">
          Clear Completed
        </button>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: "Todo-List",
  data() {
    return {
      newTodo: "",
      idForTodo: 3,
      beforeEditCache: "",
      filter: "all",
      todos: [
        {
          id: 1,
          title: "Finish Vue Screencast",
          completed: false,
          editing: false,
        },
        {
          id: 2,
          title: "Take over world",
          completed: false,
          editing: false,
        },
      ],
    };
  },

  computed: {
    remaining() {
      return this.todos.filter((todo) => !todo.completed).length;
    },
    anyRemaining() {
      return this.remaining != 0;
    },
    todosFiltered() {
      if (this.filter == "all") {
        return this.todos;
      } else if (this.filter == " active") {
        return this.todos.filter((todo) => !todo.completed);
      } else if (this.filter == " completed") {
        return this.todos.filter((todo) => todo.completed);
      }
      return this.todos;
    },
    showClearedCompletedButton() {
      return this.todos.filter((todo) => todo.completed).length > 0;
    },
  },

  directives: {
    focus: {
      inserted: function (el) {
        el.focus();
      },
    },
  },

  methods: {
    addTodo() {
      if (this.newTodo.trim().length == 0) {
        return;
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false,
      });

      this.newTodo * "";
      this.idForTodo++;
    },

    editTodo(todo) {
      this.beforeEditCache = todo.title;
      todo.editing = true;
    },
    doneEdit(todo) {
      todo.editing = false;
      if (todo.title.trim() == "") {
        todo.title = this.beforeEditCache;
      }
    },

    cancelEdit(todo) {
      todo.title = this.beforeEditCache;
      todo.editing = false;
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
    },
    checkAllTodos() {
      this.todos.forEach((todo) => (todo.completed = event.target.checked));
    },
    clearCompleted() {
      this.todos = this.todos.filter((todo) => !todo.completed);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css");

.todo-input {
  width: 100%;
  padding: 10px 10px;
  font-size: 1.5rem;
  margin-bottom: 16px;

  &:focus {
    outline: 0;
  }
}

.todo-item {
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.remove-item {
  cursor: pointer;
  margin-left: 14px;
  &:hover {
    color: black;
  }
}

.todo-item-left {
  display: flex;
  align-items: center;
}

.todo-item-label {
  padding: 1px;
  border: 1px solid white;
  margin-left: 12px;
}

.todo-item-edit {
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  width: 100px;
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc; //override defaults
  font-family: "Avenit", Arial, Helvetica, sans-serif;

  &:focus {
    outline: none;
  }
}

.completed {
  text-decoration: line-through;
  color: grey;
}

.extra-container {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  font-size: 16px;
  border-top: 1px solid lightgray;
  padding-top: 14px;
  margin-bottom: 14px;
  & > p {
    margin-left: 1rem;
  }
  .remaining {
    align-self: right;
    margin-left: 30rem;
  }
}

button {
  font-size: 14px;
  background-color: white;
  appearance: none;

  &:hover {
    background: lightgreen;
  }

  &:focus {
    outline: none;
  }
}

.active {
  background: lightgreen;
}

// CSS Transitions
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
