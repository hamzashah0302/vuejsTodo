
<template>
  <div class="container">
    <div class="todo">
      <h2>To do: ({{ todos.length }})</h2>
      <form @submit.prevent="onSubmit" v-on:submit="onAddTodo" class="todoForm">
        <input required v-model="inputData" type="text" placeholder="Type Todo.." />
        <button type="submit">Add todo</button>
      </form>

      <draggable class="dragArea list-group w-full" :list="todos" @change="log">
        <li v-for="(item, index) in todos" v-bind:key="item.text">
          <div class="list">
            <input v-model="item.isCompleted" @click="handleCheck(index)" type="checkbox" />
            <span v-if="editId != index">{{ item.text }}</span>
            <a v-if="editId != index" @click="onRemoveTodo(index)" class="remove">Remove</a>
            <a v-if="editId != index" @click="onEditTodo(index)" class="remove">Edit</a>
            <!-- edit section -->
            <input v-if="editId == index" v-model="editTodoText" type="text" />
            <a v-if="editId == index" @click="saveEdit(item, index)" class="remove">Save</a>
          </div>
        </li>
      </draggable>

      <hr />
      <h2>
        Completed items: ({{ completed }})
      </h2>
    </div>
  </div>
</template>


<script>

import { VueDraggableNext } from 'vue-draggable-next'
export default {
  components: {
    draggable: VueDraggableNext,
  },
  data() {
    return {
      completed: 0,
      enabled: true,
      dragging: false,
      editId: null,
      editTodoText: '',
      todos: [
        { text: "Learn about coding", isCompleted: false },
        { text: "Learn about Web3", isCompleted: false },
      ]
    }
  },
  methods: {
    onAddTodo() {
      if (!this.inputData)
        return
      this.todos.push({ text: this.inputData })
      this.inputData = ''
    },

    onRemoveTodo(index) {
      if (this.todos[index].isCompleted)
        this.completed = this.completed - 1
      this.todos.splice(index, 1);
    },

    onEditTodo(index) {
      this.editTodoText = this.todos[index].text
      this.editId = index;
    },

    saveEdit(todo, index) {
      if (!this.editTodoText)
        return
      todo.text = this.editTodoText
      this.todos[index] = todo
      this.editId = null
    },

    handleCheck(index) {
      let comp = this.completed
      this.todos.map((ele, i) => {
        if (i == index) {
          let obj = { ...ele, ['isCompleted']: !ele.isCompleted }
          // handle completed todos 
          if (obj.isCompleted)
            comp++
          else
            comp--
          return obj
        }
        return ele
      })
      // this.todos.sort((x, y) => (x.isCompleted === y.isCompleted) ? 0 : x.isCompleted ? -1 : 1)
      console.log("check sorting ", comp)
      this.completed = comp
    },
    log(ele) {
      // console.log("check sorting ",ele)
    },
  }
}
</script>



<style scoped>
.container {
  display: flex;
  justify-content: center;
  flex-flow: column;
  align-items: center;
}

.todo {
  width: 26%;
}
@media (max-width: 500px) {
  .todo{
    width: 100%;
  }
}

.todoForm {
  border-bottom: 1px solid #aaa;
  display: flex;
  gap: 4px;
  padding-bottom: 1rem;
}

li {
  margin: 8px 0;
  list-style-type: none;
}

h2 {
  font-weight: bold;
  margin-bottom: 15px;
}

del {
  color: rgba(0, 0, 0, 0.3);
}

input[type="text"] {
  padding: 10px;
  width: 200px;
}

.list .remove {
  display: none;
  color: red;
}

.list:hover .remove {
  display: inline-block;
  cursor: pointer;
  margin-left: 10px;
  color: red;
  opacity: 0.5;
}
</style>
