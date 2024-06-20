<template>
  <input type="text" v-model="text" @keyup.enter="addTodo"> {{ text }}
  <button type="button" @click="addTodo">新增資料</button>

  <ul>
    <li v-for="todo in filterTodos" :key="todo.id">
        <div v-if="editTodoId === todo.id">
            <input type="text" v-model="editTodoText" @keyup.enter="completeEdit(todo)">
        </div>
        <div v-else>
            <input type="checkbox" v-model="todo.done">
            <span @dblclick="editTodo(todo)" :class="{ done: todo.done }">{{ todo.text }}</span>
            <button type="button" @click="removeTodo(todo.id)">X</button>
        </div>
    </li>
    <button type="button" :disabled="visibility === 'all'" @click="visibility = 'all'">全部</button>
    <button type="button" :disabled="visibility === 'active'" @click="visibility = 'active'">待完成</button>
    <button type="button" :disabled="visibility === 'completed'" @click="visibility = 'completed'">已完成</button>
  </ul>
</template>

<script setup>
import { computed, ref, watchEffect } from 'vue';

const localData = localStorage.getItem('hex-data') || '[]';
console.log(localData);

const text = ref('')
const todos = ref(JSON.parse(localData));

const addTodo = () => {
    console.log(text.value);
    if (text.value) {
        todos.value.push({
            id: Date.now(),
            text: text.value,
            done: false,
        });
    }
    text.value = '';
    console.log(todos.value);
}

const removeTodo = (id) => {
    console.log(id);
    // 直接定義被刪後的結果
    const removeTodoList = todos.value.filter((todo) => todo.id !== id);
    console.log(removeTodoList);
    todos.value = removeTodoList;
}

const editTodoId = ref(null);
const editTodoText = ref('');
const editTodo = (todo) => {
    editTodoId.value = todo.id;
    editTodoText.value = todo.text;
    console.log(editTodoId.value);
}

const completeEdit = (todo) => {
    const index = todos.value.findIndex((t) => t.id === todo.id);
    console.log(index);
    todos.value[index].text = editTodoText.value;

    editTodoId.value = '';
    editTodoText.value = '';
}

// 自動儲存
watchEffect(() => {
    localStorage.setItem('hex-data', JSON.stringify(todos.value));
})

//
const visibility = ref('all');

const filterTodos = computed(() => {
    if (visibility.value === 'active') {
        return todos.value.filter((todo) => !todo.done);
    } 

    if (visibility.value === 'completed') {
        return todos.value.filter((todo) => todo.done);
    }

    return todos.value;
})
</script>

<style>
.done {
    text-decoration: line-through;
}
</style>
