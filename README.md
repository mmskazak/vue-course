### **Неделя 1: Основы Vue.js**

#### **День 1: Введение и первая настройка**

**✅ Задание с пошаговыми действиями:**

1.  **В терминале** выполните `npm create vue@latest`, назовите проект `vue-todo-app` и на все вопросы ответьте "No".
2.  **В терминале** перейдите в папку проекта `cd vue-todo-app`, затем выполните `npm install`.
3.  **Откройте файл `src/App.vue`**. Полностью очистите его. Вставьте этот код:
    ```html
    <script setup>
    import TodoList from './components/TodoList.vue';
    </script>

    <template>
      <header>
        <h1>Мой список дел</h1>
      </header>
      <main>
        <TodoList />
      </main>
    </template>

    <style>
      /* Здесь можно будет добавить глобальные стили */
    </style>
    ```
4.  **Создайте файл `src/components/TodoList.vue`**. Вставьте в него этот код:
    ```html
    <template>
      <h2>Список задач</h2>
      <ul>
        <li>Выучить Vue.js</li>
        <li>Создать первое приложение</li>
        <li>Отдохнуть</li>
      </ul>
    </template>
    ```
5.  **В терминале** запустите проект командой `npm run dev` и откройте предложенную ссылку в браузере. Вы должны увидеть ваш заголовок и статический список.

-----

#### **День 2: Синтаксис шаблонов и директивы**

**✅ Задание с пошаговыми действиями:**

1.  **Откройте файл `src/components/TodoList.vue`**.
2.  Добавьте тег `<script setup>` и создайте в нём реактивный массив с задачами. Для этого импортируйте `ref` из 'vue'.
    ```html
    <script setup>
    import { ref } from 'vue';

    const todos = ref([
      { id: 1, text: 'Изучить основы Vue', completed: false },
      { id: 2, text: 'Создать первый компонент', completed: true },
      { id: 3, text: 'Понять директивы', completed: false }
    ]);
    </script>
    ```
3.  Теперь **в том же файле**, в `<template>`, замените статический `<ul>` на этот код, который использует директиву `v-for` для отрисовки данных из массива:
    ```html
    <p v-if="todos.length === 0">Пока дел нет!</p>
    <ul>
      <li v-for="todo in todos" :key="todo.id">
        {{ todo.text }}
      </li>
    </ul>
    ```

-----

#### **День 3: Обработка событий и вычисляемые свойства**

**✅ Задание с пошаговыми действиями:**

1.  **Откройте файл `src/components/TodoList.vue`**.
2.  **Внутри `<script setup>`**, добавьте `ref` для хранения текста новой задачи:
    ```javascript
    const newTodoText = ref('');
    ```
3.  Там же добавьте `computed` для подсчёта незавершённых дел. Не забудьте импортировать `computed` рядом с `ref`.
    ```javascript
    import { ref, computed } from 'vue';
    //...
    const incompleteCount = computed(() => {
      return todos.value.filter(todo => !todo.completed).length;
    });
    ```
4.  Теперь добавьте функцию `addTodo` для добавления новой задачи:
    ```javascript
    function addTodo() {
      if (newTodoText.value.trim() === '') return;
      todos.value.push({
        id: Date.now(),
        text: newTodoText.value,
        completed: false
      });
      newTodoText.value = '';
    }
    ```
5.  **В `<template>` того же файла**, прямо под заголовком `<h2>`, вставьте счётчик и форму:
    ```html
    <h3>Незавершенных дел: {{ incompleteCount }}</h3>
    <form @submit.prevent="addTodo">
      <input v-model="newTodoText" placeholder="Что нужно сделать?">
      <button>Добавить</button>
    </form>
    ```

-----

#### **День 4: Классы, стили и наблюдатели**

**✅ Задание с пошаговыми действиями:**

1.  **Откройте файл `src/components/TodoList.vue`**.
2.  **В `<template>`**, найдите строку `<li>` и измените её, добавив чекбокс и динамический класс:
    ```html
    <li v-for="todo in todos" :key="todo.id" :class="{ completed: todo.completed }">
      <input type="checkbox" v-model="todo.completed">
      <span>{{ todo.text }}</span>
    </li>
    ```
3.  **В том же файле** в самый низ добавьте тег `<style>` со стилями для выполненных задач:
    ```css
    <style>
    .completed span {
      text-decoration: line-through;
      color: #888;
    }
    li {
      list-style: none;
      margin: 10px 0;
      font-size: 18px;
    }
    </style>
    ```

-----

#### **День 5: Компоненты: Props и Events**

**✅ Задание с пошаговыми действиями:**

1.  **Создайте файл `src/components/TodoForm.vue`**. Вставьте в него этот код. Он содержит форму и логику отправки события `add-todo` родителю.
    ```html
    <script setup>
    import { ref } from 'vue';

    const newTodoText = ref('');
    const emit = defineEmits(['add-todo']);

    function addTodo() {
      if (newTodoText.value.trim() === '') return;
      emit('add-todo', newTodoText.value);
      newTodoText.value = '';
    }
    </script>

    <template>
      <form @submit.prevent="addTodo">
        <input v-model="newTodoText" placeholder="Что нужно сделать?">
        <button>Добавить</button>
      </form>
    </template>
    ```
2.  **Создайте файл `src/components/TodoItem.vue`**. Вставьте в него этот код. Он принимает задачу через `props` и отправляет события `toggle-complete` и `delete-todo`.
    ```html
    <script setup>
    const props = defineProps({
      todo: { type: Object, required: true }
    });

    const emit = defineEmits(['toggle-complete', 'delete-todo']);
    </script>

    <template>
      <li :class="{ completed: props.todo.completed }">
        <input type="checkbox" :checked="props.todo.completed" @change="emit('toggle-complete', props.todo.id)">
        <span>{{ props.todo.text }}</span>
        <button @click="emit('delete-todo', props.todo.id)">Удалить</button>
      </li>
    </template>

    <style scoped>
    .completed span {
      text-decoration: line-through;
      color: #888;
    }
    li {
      list-style: none;
      margin: 10px 0;
      font-size: 18px;
    }
    button {
      margin-left: 10px;
    }
    </style>
    ```
3.  **Откройте `src/components/TodoList.vue`**. Полностью замените его содержимое на это. Теперь он просто управляет данными и использует дочерние компоненты.
    ```html
    <script setup>
    import { ref, computed } from 'vue';
    import TodoForm from './TodoForm.vue';
    import TodoItem from './TodoItem.vue';

    const todos = ref([
      { id: 1, text: 'Изучить основы Vue', completed: false },
    ]);

    const incompleteCount = computed(() => todos.value.filter(t => !t.completed).length);

    function handleNewTodo(text) {
      todos.value.push({ id: Date.now(), text: text, completed: false });
    }

    function handleToggle(id) {
      const todo = todos.value.find(t => t.id === id);
      if (todo) todo.completed = !todo.completed;
    }

    function handleDelete(id) {
      todos.value = todos.value.filter(t => t.id !== id);
    }
    </script>

    <template>
      <h2>Список задач (Незавершенных: {{ incompleteCount }})</h2>
      <TodoForm @add-todo="handleNewTodo" />
      <p v-if="todos.length === 0">Пока дел нет!</p>
      <ul>
        <TodoItem 
          v-for="todo in todos"
          :key="todo.id"
          :todo="todo"
          @toggle-complete="handleToggle"
          @delete-todo="handleDelete"
        />
      </ul>
    </template>
    ```

-----

### **Неделя 2: Продвинутые концепции и экосистема**

#### **День 9: Введение в управление состоянием с Pinia**

**✅ Задание с пошаговыми действиями (тот самый пункт, максимально подробно):**

1.  **В терминале** установите Pinia: `npm install pinia`.
2.  **Откройте `src/main.js`**. Подключите Pinia к вашему приложению.
    ```javascript
    import { createApp } from 'vue'
    import { createPinia } from 'pinia' // 1. Импортировать
    import App from './App.vue'

    const app = createApp(App)

    app.use(createPinia()) // 2. Использовать
    app.mount('#app')
    ```
3.  **Создайте папку `src/stores`**. Внутри неё создайте файл `todoStore.js`. Вставьте в него этот код. Это основа вашего хранилища.
    ```javascript
    import { defineStore } from 'pinia';
    import { ref, computed } from 'vue';

    export const useTodoStore = defineStore('todo', () => {
      // Здесь будет наша логика
      return {};
    });
    ```
4.  **Откройте `src/components/TodoList.vue`**.
      * **Найдите и вырежьте (Ctrl+X)** весь этот блок кода из `<script setup>`:
        ```javascript
        const todos = ref([
          { id: 1, text: 'Изучить основы Vue', completed: false },
        ]);

        const incompleteCount = computed(() => todos.value.filter(t => !t.completed).length);

        function handleNewTodo(text) {
          todos.value.push({ id: Date.now(), text: text, completed: false });
        }

        function handleToggle(id) {
          const todo = todos.value.find(t => t.id === id);
          if (todo) todo.completed = !todo.completed;
        }

        function handleDelete(id) {
          todos.value = todos.value.filter(t => t.id !== id);
        }
        ```
5.  **Откройте `src/stores/todoStore.js`**.
      * **Вставьте (Ctrl+V)** скопированный код внутрь функции `defineStore`.
      * **Переименуйте** функции-обработчики, чтобы они были более общими (`handleNewTodo` -\> `addTodo`, `handleToggle` -\> `toggleComplete`, `handleDelete` -\> `deleteTodo`).
      * **Верните** все эти переменные и функции из хранилища. Ваш файл `todoStore.js` должен выглядеть так:
        ```javascript
        import { defineStore } from 'pinia';
        import { ref, computed } from 'vue';

        export const useTodoStore = defineStore('todo', () => {
          // STATE
          const todos = ref([{ id: 1, text: 'Изучить основы Vue', completed: false }]);

          // GETTERS
          const incompleteCount = computed(() => todos.value.filter(t => !t.completed).length);

          // ACTIONS
          function addTodo(text) {
            todos.value.push({ id: Date.now(), text: text, completed: false });
          }

          function toggleComplete(id) {
            const todo = todos.value.find(t => t.id === id);
            if (todo) todo.completed = !todo.completed;
          }

          function deleteTodo(id) {
            todos.value = todos.value.filter(t => t.id !== id);
          }

          return { todos, incompleteCount, addTodo, toggleComplete, deleteTodo };
        });
        ```
6.  **Вернитесь в `src/components/TodoList.vue`**. Его `<script setup>` теперь пуст. Давайте подключим хранилище. Замените всё содержимое `<script setup>` на это:
    ```javascript
    import { useTodoStore } from '../stores/todoStore.js';
    import TodoForm from './TodoForm.vue';
    import TodoItem from './TodoItem.vue';

    // Получаем доступ к нашему хранилищу
    const store = useTodoStore();
    ```
7.  **В том же файле, в `<template>`**, замените все локальные вызовы на вызовы из `store`.
      * `{{ incompleteCount }}` -\> `{{ store.incompleteCount }}`
      * `@add-todo="handleNewTodo"` -\> `@add-todo="store.addTodo"`
      * `v-for="todo in todos"` -\> `v-for="todo in store.todos"`
      * `@toggle-complete="handleToggle"` -\> `@toggle-complete="store.toggleComplete"`
      * `@delete-todo="handleDelete"` -\> `@delete-todo="store.deleteTodo"`
8.  **Результат:** Приложение работает как раньше, но теперь вся логика находится в `todoStore.js`, и любой компонент может к ней обратиться. Это и есть **централизованное управление состоянием**.
