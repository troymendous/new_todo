<template>
  <div>
    <input
      class="todo-input"
      placeholder="What needs to be done?"
      v-model="newTodo"
    />
    <div v-for="todo in todos" :key="todo.id" class="todo-item">
      {{ todo.title }}
    </div>
  </div>
</template>

<script>
export default {
  name: "Todo-List",
  data() {
    return {
      newTodo: "",
      todos: [
        {
          id: 1,
          title: "Finish Vue Screencast",
          completed: false,
        },
        {
          id: 2,
          title: "Take over world",
          completed: true,
        },
      ],
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
.todo-input {
  width: 100%;
  padding: 10px 10px;
  font-size: 10px;
  margin-bottom: 16px;

  &:focus {
    outline: 0;
  }
}
</style>
