<template>
  <div class="mx-auto max-w-[720px]">
    <ul class="flex border-b mb-4">
      <li
        class="px-5 py-2 cursor-pointer hover:bg-gray-100"
        :class="tab === TAB_TYPE.tasks ? 'text-primary' : ''"
        @click="tab = TAB_TYPE.tasks">
        <h2 class="text-2xl font-bold">My tasks</h2>
      </li>
      <li
        class="px-5 py-2 cursor-pointer hover:bg-gray-100"
        :class="tab === TAB_TYPE.todo ? 'text-primary' : ''"
        @click="tab = TAB_TYPE.todo">
        <h2 class="text-2xl font-bold">In progress</h2>
      </li>
      <li
        class="px-5 py-2 cursor-pointer hover:bg-gray-100"
        :class="tab === TAB_TYPE.completed ? 'text-primary' : ''"
        @click="tab = TAB_TYPE.completed">
        <h2 class="text-2xl font-bold">Completed</h2>
      </li>
    </ul>
    <div class="flex items-center">
      <input type="text" name="title" placeholder="todo" v-model="todo.title">
      <textarea name="description" placeholder="description" v-model="todo.description" cols="40" rows="3"></textarea>
      <!-- <input type="date" name="date" v-model="todo.deadline"> -->
    </div>
    <button
      class="py-2 px-3 mb-4 rounded"
      :class="disabledAddBtn ? 'bg-gray-300 cursor-not-allowed' : 'bg-primary text-white'"
      :disabled="disabledAddBtn"
      @click="addTodoItem"
    >add</button>

    <h2 class="text-primary text-3xl font-bold">List:</h2>
    <ul>
      <li class="flex items-center" v-for="(todoItem, index) in filterTodo(todoList, tab)" :key="todoItem.id">
        <template v-if="editedId === todoItem.id">
          <input type="text" v-model="editedTodo.title" class="ml-7">
          <button class="py-2 px-3 ml-4 hover:bg-gray-100" @click="confirmEdited(index)">Confirm</button>
          <button class="py-2 px-3 ml-4 hover:bg-gray-100" @click="cancelEdited(index)">Cancel</button>
        </template>
        <template v-else>
          <input type="checkbox" class="mr-4" name="todo" :id="todoItem.id" v-model="todoItem.isChecked">
          <label
            :for="todoItem.id"
            class="text-2xl font-bold cursor-pointer"
            :class="todoItem.isChecked ? 'line-through text-gray-300' : ''"
          >{{ todoItem.title }}</label>
          <button class="py-2 px-3 ml-4 hover:bg-gray-100" @click="enterEdited(todoItem)">Edit</button>
          <button class="py-2 px-3 ml-4 hover:bg-gray-100" @click="deleteTodo(index)">Delete</button>
        </template>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
import { useStorage } from '@vueuse/core'
// import dayjs from 'dayjs'

/**
 * section 1
 * - tab, 切換 todo-list, completed
 * - if todo-item's isChecked is true, move to completed tab
 * - if todo-item's isChecked is false, move to todo-list tab
 * - @actiton
 *  - click tab to switch tab name
 */
const TAB_TYPE = {
  tasks: 'tasks',
  todo: 'todo',
  completed: 'completed',
}
const tab = ref(TAB_TYPE.tasks)

/**
 * section 2
 * - input: todo text
 * - textarea: content text
 * - input-date: deadline
 * - button: add todoItem
 * - todo-list-Array: todo-item object:
 *  - text
 *  - content
 *  - deadline
 *  - isChecked
 * - @action
 *  - enter confirm,
 *    - unshift a todo-item to todo-list-Array
 */
const todo = ref({
  title: '',
  description: '',
  // deadline: dayjs(new Date()).format('YYYY-MM-DD'),
})
const todoList = useStorage('todo-list', [])
const disabledAddBtn = ref(true)

watch(
  () => ({ ...todo.value }),
  (value) => {
    if (value.title && value.description) {
      disabledAddBtn.value = false
    } else {
      disabledAddBtn.value = true
    }
  },
  {
    deep: true
  }
)

const addTodoItem = () => {
  todoList.value.push({
    ...todo.value,
    id: Math.random().toString(36).substr(2, 4),
    isChecked: false,
  })

  todo.value = {
    title: '',
    description: '',
    // deadline: '',
  }
}

/**
 * section 3
 * - isEdited
 * - edit-todo-item object:
 *  - text
 *  - content
 *  - deadline
 *  - @action
 *    - enter edit,
 *      - change isEdited to true, copy todo-item to edit-todo-item
 *    - cancel edit,
 *      - change isEdited to false
 *    - confirm edit,
 *      - change isEdited to false, copy edit-todo-item to todo-item
 *    - delete todo
 */
const editedId = ref(null)
const editedTodo = ref({
  title: '',
  description: '',
  // deadline: '',
})

const enterEdited = (todoItem) => {
  editedId.value = todoItem.id
  editedTodo.value = {
    title: todoItem.title,
    description: todoItem.description,
    // deadline: todoItem.deadline,
  }
}
const cancelEdited = () => {
  editedId.value = null
}

const confirmEdited = (todoItemIndex) => {
  const todoItem = todoList.value[todoItemIndex]
  todoList.value.splice(todoItemIndex, 1, {
    ...todoItem,
    ...editedTodo.value,
  })
  editedId.value = null
}

const deleteTodo = (todoItemIndex) => {
  todoList.value.splice(todoItemIndex, 1)
}

/**
 * section 4
 * - @action
 *  - checked,
 *    - if isChecked is true that will be move to completed.
 *    - if isChecked is false that will be move to todo-list.
 */
const filterTodo = (list = [], tabState = '') => list.filter((item) => {
  switch (tabState) {
    case TAB_TYPE.todo:
      return !item.isChecked
    case TAB_TYPE.completed:
      return item.isChecked
    case TAB_TYPE.tasks:
      return true
    default:
      return true
  }
})
</script>

