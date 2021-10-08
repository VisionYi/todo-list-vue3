<template>
  <div class="mx-auto max-w-[720px]">
    <ul class="flex">
      <li class="px-5 py-2 cursor-pointer hover:bg-gray-100">
        <h2 class="text-2xl font-bold">Todo-list</h2>
      </li>
      <li class="px-5 py-2 cursor-pointer hover:bg-gray-100">
        <h2 class="text-2xl font-bold">Completed</h2>
      </li>
    </ul>


    <input type="text" v-model="text" class="border">
    {{ text }}
  </div>
</template>

<script>
import { ref } from 'vue'

/**
 * section 1
 * - tab, 切換 todo-list, completed
 * - if todo-item's isChecked is true, on completed
 * - if todo-item's isChecked is false, on todo-list
 *
 * section 2
 * - input: todo text
 * - textarea: content text
 * - input-date: deadline
 * - @action:
 *  - enter confirm,
 *    - unshift a todo-item to todo-list-Array
 *
 * section 3
 * - isEdited
 * - edit-todo-item object:
 *  - text
 *  - content
 *  - deadline
 * - todo-list-Array: todo-item object:
 *  - text
 *  - content
 *  - deadline
 *  - isChecked
 *  - @action:
 *    - checked,
 *      - if isChecked is true that will be move to completed.
 *      - if isChecked is false that will be move to todo-list.
 *    - enter edit,
 *      - change isEdited to true, copy todo-item to edit-todo-item
 *    - cancel edit,
 *      - change isEdited to false
 *    - confirm edit,
 *      - change isEdited to false, copy edit-todo-item to todo-item
 *    - delete
 */
export default {
  name: 'App',
  setup() {
    const text = ref('')
    return {
      text
    }
  }
}
</script>

<style lang="scss">
$color: #2c3e50;

</style>
