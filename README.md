### **Неделя 1: Основы Vue.js**

#### **День 1: Введение и первая настройка**

**Цель:** Создать структуру проекта и отобразить первый статический компонент.

1.  **Создайте проект:** В терминале выполните команду `npm create vue@latest`. Назовите проект `vue-todo-app` и выберите "No" для всех опций (TypeScript, JSX, Router, Pinia и т.д. — мы добавим их позже вручную).
2.  **Перейдите в папку проекта:** `cd vue-todo-app` и установите зависимости: `npm install`.
3.  **Откройте `src/App.vue`:** Удалите всё содержимое тегов `<template>` и `<script setup>`. Также удалите тег `<style>`.
4.  **В `<template>` файла `App.vue`** добавьте заголовок:
    ```html
    <template>
      <header>
        <h1>Мой список дел</h1>
      </header>
      <main>
        </main>
    </template>
    ```
5.  **Создайте новый файл:** В папке `src/components/` создайте файл `TodoList.vue`.
6.  **В `TodoList.vue`** добавьте базовую разметку со статичным списком:
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
7.  **Вернитесь в `App.vue`:** Теперь нужно импортировать и использовать наш новый компонент.
      * Добавьте тег `<script setup>` и внутри него импортируйте `TodoList`:
        ```javascript
        import TodoList from './components/TodoList.vue';
        ```
      * В `<template>` внутри тега `<main>` вставьте компонент: `<TodoList />`.
8.  **Запустите проект:** В терминале выполните `npm run dev` и откройте ссылку в браузере. Вы должны увидеть заголовок из `App.vue` и статический список из `TodoList.vue`.

-----

#### **День 2: Синтаксис шаблонов и директивы**

**Цель:** Превратить статический список дел в динамический, который отображает данные из массива.

1.  **Откройте `src/components/TodoList.vue`**.
2.  **Добавьте `<script setup>`** и импортируйте `ref` из `vue`. `ref` делает переменную реактивной.
3.  **Создайте реактивный массив `todos`** внутри `<script setup>`:
    ```javascript
    import { ref } from 'vue';

    const todos = ref([
      { id: 1, text: 'Изучить основы Vue', completed: false },
      { id: 2, text: 'Создать первый компонент', completed: true },
      { id: 3, text: 'Понять директивы', completed: false }
    ]);
    ```
4.  **Измените `<template>`:** Удалите статический `<ul>`.
5.  **Создайте новый `<ul>`**. Внутри него добавьте один `<li>`, к которому примените директиву `v-for` для перебора массива `todos`.
      * `<li v-for="todo in todos" :key="todo.id">`
      * Директива `:key="todo.id"` обязательна для Vue, чтобы эффективно отслеживать элементы списка.
6.  **Внутри `<li>`** используйте `{{ }}` для вывода текста задачи: `{{ todo.text }}`.
7.  **Добавьте условный рендеринг:** Над тегом `<ul>` добавьте параграф, который будет виден только если список пуст.
      * `<p v-if="todos.length === 0">Пока дел нет!</p>`
8.  **Результат:** Ваш компонент теперь динамически отображает задачи из массива `todos`.

-----

#### **День 3: Обработка событий и вычисляемые свойства**

**Цель:** Добавить форму для создания новых задач и счетчик незавершенных дел.

1.  **Откройте `src/components/TodoList.vue`**.
2.  **В `<script setup>`** создайте новую `ref` для текста в поле ввода:
    ```javascript
    const newTodoText = ref('');
    ```
3.  **В `<template>`** над списком `<ul>` добавьте форму:
    ```html
    <form @submit.prevent="addTodo">
      <input v-model="newTodoText" placeholder="Что нужно сделать?">
      <button>Добавить</button>
    </form>
    ```
      * `@submit.prevent` — это сокращение для `v-on:submit` с модификатором `.prevent`, который отменяет стандартное поведение формы (перезагрузку страницы).
      * `v-model="newTodoText"` связывает значение поля ввода с нашей переменной.
4.  **В `<script setup>`** создайте метод `addTodo`:
    ```javascript
    function addTodo() {
      if (newTodoText.value.trim() === '') return; // Не добавлять пустые задачи

      todos.value.push({
        id: Date.now(), // Простой способ получить уникальный id
        text: newTodoText.value,
        completed: false
      });
      newTodoText.value = ''; // Очищаем поле ввода
    }
    ```
5.  **Создайте вычисляемое свойство:** Импортируйте `computed` из `vue`.
    ```javascript
    import { ref, computed } from 'vue';
    // ...
    const incompleteCount = computed(() => {
      return todos.value.filter(todo => !todo.completed).length;
    });
    ```
6.  **Отобразите счетчик** в `<template>`, например, под заголовком:
      * `<h3>Незавершенных дел: {{ incompleteCount }}</h3>`
7.  **Результат:** Теперь вы можете вводить текст, нажимать "Добавить", и новая задача появится в списке. Счетчик будет обновляться автоматически.

-----

#### **День 4: Классы, стили и наблюдатели**

**Цель:** Стилизовать выполненные задачи и добавить чекбоксы для изменения их статуса.

1.  **Откройте `src/components/TodoList.vue`**.
2.  **Обновите `<li>` в шаблоне:** Добавьте чекбокс и привяжите его к свойству `completed` задачи.
    ```html
    <li v-for="todo in todos" :key="todo.id" :class="{ completed: todo.completed }">
      <input type="checkbox" v-model="todo.completed">
      <span>{{ todo.text }}</span>
    </li>
    ```
      * `:class="{ completed: todo.completed }"` — синтаксис для динамического добавления класса. Класс `completed` будет добавлен к `<li>`, только если `todo.completed` равно `true`.
3.  **Добавьте стили:** В том же файле `TodoList.vue` добавьте тег `<style>`.
    ```css
    <style>
    .completed span {
      text-decoration: line-through;
      color: #888;
    }
    li {
      list-style: none;
      margin: 5px 0;
    }
    </style>
    ```
4.  **(Необязательно) Добавьте наблюдатель:** Импортируйте `watch` из `vue` и добавьте в `<script setup>`:
    ```javascript
    import { ref, computed, watch } from 'vue';
    // ...
    watch(todos, () => {
      console.log('Список дел был обновлен!');
    }, { deep: true }); // { deep: true } нужен для отслеживания изменений внутри объектов в массиве
    ```
5.  **Результат:** Теперь у каждой задачи есть чекбокс. При его включении текст задачи будет перечеркиваться. Откройте консоль разработчика в браузере — при любом изменении списка там будет появляться сообщение.

-----

#### **День 5: Компоненты: Props и Events**

**Цель:** Разделить приложение на логические части (компоненты) для лучшей организации кода.

1.  **Создайте `src/components/TodoForm.vue`:**
      * **Перенесите** HTML-код формы (`<form>...</form>`) из `TodoList.vue` в `<template>` этого файла.
      * **Перенесите** `ref` для `newTodoText` в `<script setup>`.
      * **Создайте `$emit`:** Компонент не должен сам менять данные родителя. Он должен "попросить" об этом.
        ```javascript
        // В TodoForm.vue
        const emit = defineEmits(['add-todo']);

        function addTodo() {
          if (newTodoText.value.trim() === '') return;
          emit('add-todo', newTodoText.value); // Отправляем событие и текст
          newTodoText.value = '';
        }
        ```
2.  **Создайте `src/components/TodoItem.vue`:**
      * **Перенесите** HTML-код одного элемента списка (`<li>...</li>`) из `TodoList.vue` в `<template>` этого файла.
      * **Определите `props`:** Компонент должен получать данные о задаче от родителя.
        ```javascript
        // В TodoItem.vue
        const props = defineProps({
          todo: {
            type: Object,
            required: true
          }
        });
        const emit = defineEmits(['toggle-complete', 'delete-todo']);
        ```
      * **Адаптируйте шаблон:** Замените `todo` на `props.todo`. Для чекбокса используйте `@change` вместо `v-model`, чтобы отправить событие.
        ```html
        <li :class="{ completed: props.todo.completed }">
          <input type="checkbox" :checked="props.todo.completed" @change="emit('toggle-complete', props.todo.id)">
          <span>{{ props.todo.text }}</span>
          <button @click="emit('delete-todo', props.todo.id)">Удалить</button>
        </li>
        ```
3.  **Обновите `src/components/TodoList.vue`:**
      * **Удалите** из него все, что вы перенесли.
      * **Импортируйте** `TodoForm` и `TodoItem`.
      * **Используйте компоненты в шаблоне**, передавая данные и слушая события:
        ```html
        <TodoForm @add-todo="handleNewTodo" />
        <ul>
          <TodoItem
            v-for="todo in todos"
            :key="todo.id"
            :todo="todo"
            @toggle-complete="handleToggle"
            @delete-todo="handleDelete"
          />
        </ul>
        ```
      * **Создайте методы-обработчики** в `<script setup>`, которые будут изменять массив `todos`:
        ```javascript
        // В TodoList.vue
        function handleNewTodo(text) { /* Логика добавления */ }
        function handleToggle(id) { /* Логика изменения статуса */ }
        function handleDelete(id) { /* Логика удаления */ }
        ```
4.  **Результат:** Внешне приложение работает так же, но его код теперь гораздо чище и лучше организован.

-----

### **Неделя 2: Продвинутые концепции и экосистема**

#### **День 6: Слоты и жизненный цикл компонента**

**Цель**: Создать универсальный компонент-обертку и использовать хуки жизненного цикла.

1.  **Создайте `src/components/BaseCard.vue`**:
      * Внутри `<template>` создайте `div` с классом, например, `card`.
      * Внутрь этого `div` поместите тег `<slot></slot>`. Он будет служить "контейнером" для контента.
      * Добавьте стили для класса `card` (рамка, отступы, тень).
        ```html
        <template>
          <div class="card">
            <slot></slot>
          </div>
        </template>
        <style>
        .card {
          border: 1px solid #ddd;
          border-radius: 8px;
          padding: 20px;
          margin-bottom: 10px;
          box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        </style>
        ```
2.  **Откройте `src/components/TodoItem.vue`**:
      * Импортируйте `BaseCard.vue`.
      * **Оберните** всё содержимое `<template>` (тег `<li>`) в компонент `BaseCard`.
        ```html
        <template>
          <BaseCard>
            <li ...>
              ...
            </li>
          </BaseCard>
        </template>
        ```
3.  **Откройте `src/components/TodoList.vue`**:
      * Импортируйте `onMounted` из `vue`.
      * Внутри `<script setup>` вызовите этот хук.
        ```javascript
        import { onMounted } from 'vue';
        onMounted(() => {
          console.log('Компонент TodoList готов и отображен на странице!');
        });
        ```
4.  **Результат**: Каждый элемент списка теперь обернут в стилизованную карточку. При загрузке страницы в консоли появится ваше сообщение.

-----

#### **День 7: Введение в Vue Router**

**Цель**: Сделать приложение многостраничным (SPA), добавив навигацию.

1.  **Установите Vue Router**: В терминале выполните `npm install vue-router`.
2.  **Создайте папку `src/router`** и в ней файл `index.js`.
3.  **Настройте роутер в `src/router/index.js`**:
    ```javascript
    import { createRouter, createWebHistory } from 'vue-router';
    import TodoListPage from '../views/TodoListPage.vue';
    import AboutPage from '../views/AboutPage.vue';

    const router = createRouter({
      history: createWebHistory(),
      routes: [
        { path: '/', name: 'home', component: TodoListPage },
        { path: '/about', name: 'about', component: AboutPage }
      ]
    });

    export default router;
    ```
4.  **Создайте страницы**: Создайте папку `src/views` и в ней два файла:
      * `TodoListPage.vue`: **Перенесите** сюда всё содержимое из `src/components/TodoList.vue`. Теперь это не компонент, а целая страница.
      * `AboutPage.vue`: Создайте простую страницу с заголовком "О приложении".
5.  **Подключите роутер к приложению**: В `src/main.js` импортируйте роутер и подключите его.
    ```javascript
    import { createApp } from 'vue'
    import App from './App.vue'
    import router from './router' // Импорт

    const app = createApp(App)
    app.use(router) // Использование
    app.mount('#app')
    ```
6.  **Обновите `App.vue`**: Добавьте навигацию и место для отображения страниц.
    ```html
    <template>
      <header>
        <h1>Мой список дел</h1>
        <nav>
          <router-link to="/">Главная</router-link> |
          <router-link to="/about">О нас</router-link>
        </nav>
      </header>
      <main>
        <router-view></router-view> </main>
    </template>
    ```
7.  **Результат**: Теперь у вас есть навигация. При клике на ссылки URL меняется, и подгружается соответствующая страница без перезагрузки.

-----

#### **День 8: Динамические маршруты и программная навигация**

**Цель**: Создать отдельную страницу для просмотра деталей каждой задачи.

1.  **Создайте страницу `src/views/TodoDetailPage.vue`**:
      * В `<template>` добавьте заголовок `<h2>Детали задачи</h2>` и место для вывода информации.
      * В `<script setup>` получите доступ к параметрам роутера.
        ```javascript
        import { useRoute } from 'vue-router';
        const route = useRoute();
        const todoId = route.params.id; // Получаем id из URL
        ```
      * Отобразите `todoId` в шаблоне: `<p>ID Задачи: {{ todoId }}</p>`.
2.  **Обновите `src/router/index.js`**: Добавьте новый динамический маршрут.
    ```javascript
    //...
    import TodoDetailPage from '../views/TodoDetailPage.vue';
    //...
    routes: [
      // ...,
      { path: '/todo/:id', name: 'todo-detail', component: TodoDetailPage }
    ]
    ```
3.  **Откройте `src/components/TodoItem.vue`**: Превратите текст задачи в ссылку.
      * Импортируйте `useRouter` из `vue-router`.
      * `const router = useRouter();`
      * Создайте метод для перехода: `function goToDetails() { router.push(`/todo/${props.todo.id}`); }`
      * Оберните `<span>{{ props.todo.text }}</span>` в `div` и добавьте событие: `<div @click="goToDetails" style="cursor: pointer;">...</div>`
4.  **Результат**: Текст каждой задачи теперь кликабелен. При клике вы перейдете на новую страницу (например, `/todo/1`), где будет показан ID этой задачи.

-----

#### **День 9: Введение в управление состоянием с Pinia**

**Цель**: Вынести всю логику управления задачами в централизованное хранилище.

1.  **Установите Pinia**: `npm install pinia`.
2.  **Подключите Pinia в `src/main.js`**:
    ```javascript
    import { createPinia } from 'pinia';
    //...
    app.use(createPinia()); // Перед app.mount()
    ```
3.  **Создайте хранилище**: Создайте папку `src/stores` и в ней файл `todoStore.js`.
    ```javascript
    import { defineStore } from 'pinia';
    import { ref, computed } from 'vue';

    export const useTodoStore = defineStore('todo', () => {
      // State
      const todos = ref([]);
      // Getters
      const incompleteCount = computed(() => todos.value.filter(t => !t.completed).length);
      // Actions
      function addTodo(text) { /* ... */ }
      function deleteTodo(id) { /* ... */ }
      function toggleTodo(id) { /* ... */ }

      return { todos, incompleteCount, addTodo, deleteTodo, toggleTodo };
    });
    ```
4.  **Перенесите логику**:
      * **Переместите** массив `todos` и всю логику его изменения (`addTodo`, `deleteTodo` и т.д.) из `TodoListPage.vue` в `todoStore.js`.
5.  **Используйте хранилище в `TodoListPage.vue`**:
      * Удалите из него всю локальную логику.
      * Импортируйте и используйте `store`.
        ```javascript
        import { useTodoStore } from '../stores/todoStore';
        const store = useTodoStore();
        // Теперь вы можете использовать store.todos, store.incompleteCount, store.addTodo() и т.д.
        ```
6.  **Адаптируйте компоненты**: Убедитесь, что `TodoListPage`, `TodoForm` и `TodoItem` теперь вызывают `actions` из `store` для всех операций. Например, `TodoForm` будет вызывать `store.addTodo(text)`.
7.  **Результат**: Приложение работает как раньше, но теперь состояние не "размазано" по компонентам, а находится в одном месте, что упрощает управление им.

-----

#### **День 10: Работа с API**

**Цель**: Загружать задачи не из статического массива, а с реального сервера.

1.  **Установите Axios**: `npm install axios`.
2.  **Откройте `src/stores/todoStore.js`**.
3.  **Создайте `action` для загрузки данных**:
    ```javascript
    import axios from 'axios';
    //...
    async function fetchTodos() {
      try {
        const response = await axios.get('https://jsonplaceholder.typicode.com/todos?_limit=15');
        todos.value = response.data; // Записываем полученные данные в state
      } catch (error) {
        console.error('Ошибка при загрузке задач:', error);
      }
    }
    //...
    // Не забудьте вернуть fetchTodos из store
    return { ..., fetchTodos };
    ```
4.  **Вызовите `action` при загрузке**: В `TodoListPage.vue` вызовите `store.fetchTodos()` в хуке `onMounted`.
    ```javascript
    import { onMounted } from 'vue';
    // ...
    onMounted(() => {
      store.fetchTodos();
    });
    ```
5.  **Адаптируйте `actions`**: Измените `addTodo`, `deleteTodo`, `toggleTodo` так, чтобы они тоже делали запросы к API (например, `axios.post`, `axios.delete`). Пока что можно просто имитировать их, выводя в консоль.
6.  **Результат**: При загрузке главной страницы список дел будет заполняться данными, полученными с сервера `jsonplaceholder`.

-----

### **Неделя 3: Composition API и лучшие практики**

Задания на эту неделю — это один большой рефакторинг вашего приложения с использованием новых концепций.

#### **Дни 11-15: Рефакторинг и продвинутые возможности**

**Цель:** Полностью переписать приложение на современный синтаксис, выделить переиспользуемую логику, добавить UX-улучшения и подготовить проект к публикации.

1.  **Рефакторинг на Composition API (Дни 11-12)**: Этот синтаксис вы уже использовали с `<script setup>`. Теперь убедитесь, что вы полностью понимаете его принципы.

      * **Задача**: Пройдитесь по всем вашим компонентам (`AboutPage`, `TodoForm`, `TodoItem`, `TodoListPage` и т.д.) и убедитесь, что вся логика находится внутри `<script setup>`.
      * Проверьте, что вы используете `ref` для примитивов и `reactive` для сложных объектов (где это уместно), `computed` для вычисляемых значений и `watch` для наблюдения.

2.  **Создание Composable (День 13)**:

      * **Задача**: Создать `composable`-функцию для инкапсуляции логики работы с API.
      * **Шаги**:
        1.  Создайте файл `src/composables/useApi.js`.
        2.  Внутри него создайте функцию `export function useApi() { ... }`.
        3.  Перенесите туда логику `fetchTodos`, `addTodo` и т.д. из Pinia store. Эта функция должна возвращать состояние (например, `const todos = ref([])`) и методы для работы с ним.
        4.  Импортируйте и используйте `useApi()` в вашем Pinia store, чтобы сделать его тоньше и чище.

3.  **Использование `<Suspense>` и `<Teleport>` (День 14)**:

      * **Задача**: Улучшить пользовательский опыт во время загрузки и при удалении задач.
      * **`<Suspense>`**:
        1.  Откройте `src/views/TodoListPage.vue`.
        2.  Оберните ваш основной компонент списка (`<TodoList>`) в `<Suspense>`.
        3.  Добавьте `fallback` шаблон, который будет показан во время загрузки данных.
            ```html
            <Suspense>
              <template #default>
                <TodoList />
              </template>
              <template #fallback>
                <p>Загрузка списка дел...</p>
              </template>
            </Suspense>
            ```
      * **`<Teleport>`**:
        1.  Создайте компонент `src/components/Modal.vue` с базовой разметкой модального окна (темный фон, белое окно по центру с кнопками "Да" / "Нет").
        2.  Оберните корневой элемент модального окна в `<Teleport to="body">`. Это заставит его рендериться в конце `<body>`, а не там, где он был вызван.
        3.  В `TodoItem.vue` добавьте `ref` для управления видимостью модального окна (`showModal = ref(false)`).
        4.  При клике на кнопку "Удалить" устанавливайте `showModal.value = true`.
        5.  В шаблоне `TodoItem.vue` добавьте `<Modal v-if="showModal" @confirm="handleDelete" @cancel="showModal = false" />`.

4.  **Сборка и деплой (День 15)**:

      * **Задача**: Подготовить приложение для публикации в интернете.
      * **Шаги**:
        1.  **Остановите** сервер разработки (Ctrl+C).
        2.  **Выполните** в терминале команду `npm run build`. Эта команда создаст оптимизированную версию вашего приложения в новой папке `dist`.
        3.  **Изучите** папку `dist`. Вы увидите минифицированные `.js` и `.css` файлы — это и есть ваше готовое приложение.
        4.  **(Бонус)** Зарегистрируйтесь на [Netlify](https://www.netlify.com/). Просто перетащите вашу папку `dist` в их веб-интерфейс. Через минуту ваше приложение будет доступно онлайн по уникальной ссылке.
