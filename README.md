# Vue 3 从零基础到独立开发项目完全教程

## 一、前置说明
本教程面向具备 HTML、CSS、JavaScript（ES6+）基础的开发者，全程采用 Vue 3 推荐的组合式 API，分为入门基础、核心概念、进阶工具、项目实践四个阶段，助力从零基础掌握 Vue 3 并能独立开发中型项目。

### 学习建议
- 环境要求：Node.js 18+、VS Code（安装 Volar 插件）
- 项目创建：使用 Vite（`npm create vue@latest`），边学边敲代码
- 官方文档：Vue 3 中文文档（随时查阅）
- 时间预估：入门 2-3 天、核心 3-5 天、进阶 2-3 天、项目 1 周

## 二、阶段 1：入门基础（快速上手）
### 目标
创建第一个 Vue 应用，理解响应式和模板核心特性。

### 1.1 安装与项目创建
使用 Vite 快速创建项目：
```bash
npm create vue@latest my-vue-app
cd my-vue-app
npm install
npm run dev
创建选项建议：
TypeScript：No（先掌握基础）
JSX：No
Router：稍后选 Yes
Pinia：稍后选 Yes
Vitest：No
ESLint：No
核心目录结构：
plaintext
my-vue-app/
├── src/
│   ├── components/    # 组件目录
│   ├── App.vue        # 根组件
│   └── main.js        # 项目入口
├── public/            # 静态资源
└── package.json       # 依赖配置
练习：
运行 npm run dev，访问 http://localhost:5173 查看默认页面。
1.2 第一个应用：Hello World
修改 src/App.vue：
vue
<template>
  <div>
    <h1>{{ message }}</h1>
    <button @click="updateMessage">更新消息</button>
  </div>
</template>

<script setup>
import { ref } from 'vue';

// 响应式变量
const message = ref('Hello Vue 3!');

// 事件处理函数
const updateMessage = () => {
  message.value = '消息已更新！'; // 响应式数据更新，UI 自动刷新
};
</script>

<style>
h1 { color: blue; }
</style>
核心解释：
<template>：HTML 模板区域，{{ }} 为插值语法，支持表达式
<script setup>：组合式 API 简洁语法，变量 / 函数自动暴露给模板
ref()：创建基本类型响应式数据，需通过 .value 访问 / 修改
@click：v-on:click 的缩写，绑定点击事件
练习：
添加输入框，通过 v-model 双向绑定 message。
1.3 模板语法基础
语法类型	示例	说明
文本插值	{{ count + 1 }}	支持任意合法 JS 表达式
属性绑定	<img :src="imageUrl" />	v-bind 缩写，绑定属性
条件渲染	<p v-if="isVisible">显示</p>	根据条件展示 / 隐藏内容
列表渲染	<li v-for="item in list" :key="item.id">{{ item.name }}</li>	循环渲染列表，key 必传
事件绑定	<button @click="handleClick($event)">点击</button>	绑定事件，$event 为事件对象
练习：
构建一个列表（渲染 3 个物品），添加按钮切换列表显示 / 隐藏。
三、阶段 2：核心概念（构建组件化应用）
目标
掌握组件开发、响应式原理、生命周期等核心能力。
2.1 组件创建与通信
（1）基础组件开发
创建 src/components/Counter.vue：
vue
<template>
  <div>
    <p>计数：{{ count }}</p>
    <button @click="$emit('increment')">+1</button>
  </div>
</template>

<script setup>
import { ref } from 'vue';

// 接收父组件传递的 props
defineProps(['initialValue']);
// 声明向父组件派发的事件
defineEmits(['increment']);

const count = ref(0);
</script>
（2）父组件使用子组件（App.vue）
vue
<template>
  <Counter :initial-value="5" @increment="handleIncrement" />
  <p>父组件计数：{{ parentCount }}</p>
</template>

<script setup>
import { ref } from 'vue';
import Counter from './components/Counter.vue';

const parentCount = ref(0);
const handleIncrement = () => {
  parentCount.value++;
};
</script>
（3）插槽（Slots）：组件内容分发
子组件（MyCard.vue）：
vue
<template>
  <div class="card">
    <!-- 具名插槽 -->
    <slot name="header"></slot>
    <!-- 默认插槽 -->
    <slot></slot>
  </div>
</template>
父组件使用：
vue
<template>
  <MyCard>
    <template #header>
      <h2>卡片标题</h2>
    </template>
    <p>卡片正文内容</p>
  </MyCard>
</template>

<script setup>
import MyCard from './components/MyCard.vue';
</script>
核心解释：
defineProps：接收父组件数据（单向数据流，子组件不可直接修改）
defineEmits：子组件向父组件通信的方式
插槽：实现组件内容的灵活定制，支持具名插槽和默认插槽
练习：
创建 TodoItem 组件，接收任务文本作为 props，通过插槽自定义操作按钮。
2.2 响应式与计算属性
（1）ref vs reactive
类型	适用场景	访问方式
ref	基本类型（字符串 / 数字）	需通过 .value 访问
reactive	对象 / 数组	直接访问属性 / 元素
（2）计算属性与监听器
vue
<script setup>
import { ref, reactive, computed, watch } from 'vue';

// 基础响应式数据
const count = ref(0);
const user = reactive({ name: '张三', age: 25 });

// 计算属性：缓存结果，依赖变化时自动更新
const doubled = computed(() => count.value * 2);
const fullName = computed(() => `${user.name} - ${user.age}岁`);

// 监听器：监听数据变化执行逻辑
watch(() => count.value, (newVal, oldVal) => {
  console.log(`计数从 ${oldVal} 变为 ${newVal}`);
});
</script>
练习：
用 computed 计算 Todo 列表的完成率（已完成数量 / 总数量）。
2.3 生命周期钩子
组合式 API 中使用 onXxx 形式的钩子函数，核心钩子如下：
钩子函数	执行时机	典型用例
onBeforeMount	组件挂载前（模板编译后）	初始化数据
onMounted	组件挂载完成后	发送 AJAX 请求、操作 DOM
onUpdated	数据更新导致 DOM 更新后	响应 DOM 变化
onUnmounted	组件卸载后	清理定时器、取消事件监听
示例：
vue
<script setup>
import { onMounted, onUnmounted } from 'vue';

let timer;
// 挂载后启动定时器
onMounted(() => {
  timer = setInterval(() => console.log('tick'), 1000);
});
// 卸载前清理定时器
onUnmounted(() => clearInterval(timer));
</script>
练习：
组件挂载时从 localStorage 加载数据，卸载时将数据保存到 localStorage。
四、阶段 3：进阶工具（构建真实应用）
目标
集成路由、状态管理，掌握项目构建与部署。
3.1 Vue Router（路由）
（1）安装
bash
运行
npm install vue-router@4
（2）配置路由（src/router/index.js）
javascript
运行
import { createRouter, createWebHistory } from 'vue-router';
import Home from '../views/Home.vue';
import About from '../views/About.vue';

// 路由规则
const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About }
];

// 创建路由实例
const router = createRouter({
  history: createWebHistory(), // HTML5 历史模式
  routes
});

export default router;
（3）全局注册（src/main.js）
javascript
运行
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';

const app = createApp(App);
app.use(router); // 注册路由
app.mount('#app');
（4）使用路由
vue
<template>
  <!-- 路由链接 -->
  <router-link to="/">首页</router-link>
  <router-link to="/about">关于</router-link>
  <!-- 路由视图：渲染匹配的组件 -->
  <router-view />
</template>
练习：
添加动态路由 /user/:id，在组件中通过 useRoute() 获取 id 参数。
3.2 状态管理：Pinia
（1）安装
bash
运行
npm install pinia
（2）创建 Store（src/stores/counter.js）
javascript
运行
import { defineStore } from 'pinia';
import { ref } from 'vue';

// 定义并导出 Store
export const useCounterStore = defineStore('counter', () => {
  // 状态
  const count = ref(0);
  // 操作
  const increment = () => count.value++;
  // 暴露状态和操作
  return { count, increment };
});
（3）全局注册（src/main.js）
javascript
运行
import { createApp } from 'vue';
import { createPinia } from 'pinia';
import App from './App.vue';

const app = createApp(App);
app.use(createPinia()); // 注册 Pinia
app.mount('#app');
（4）使用 Store
vue
<template>
  <p>{{ store.count }}</p>
  <button @click="store.increment">+1</button>
</template>

<script setup>
import { useCounterStore } from '../stores/counter';

// 获取 Store 实例
const store = useCounterStore();
</script>
练习：
用 Pinia 管理全局 Todo 列表，实现添加 / 删除功能。
3.3 构建与部署
（1）开发与构建
bash
运行
# 开发环境运行
npm run dev
# 生产环境构建（生成 dist 目录）
npm run build
（2）本地预览构建结果
bash
运行
npm install -g serve
serve -s dist
（3）部署方式
简易部署：上传 dist 目录到 Vercel/Netlify/GitHub Pages
自定义配置：修改 vite.config.js 配置代理、压缩、插件等
练习：
构建项目并部署到 GitHub Pages。
五、阶段 4：项目实践（Todo App）
目标
整合所有知识，开发一个完整的 Todo 应用（路由 + 状态管理 + 持久化）。
项目功能
添加 / 编辑 / 删除 Todo 任务
标记任务完成状态
路由切换（首页 / 详情）
localStorage 数据持久化
步骤 1：项目初始化
bash
运行
# 创建项目
npm create vue@latest todo-app
cd todo-app
# 安装依赖
npm install
npm install vue-router@4 pinia
步骤 2：创建 Todo Store（src/stores/todos.js）
javascript
运行
import { defineStore } from 'pinia';
import { ref } from 'vue';

export const useTodosStore = defineStore('todos', () => {
  // 任务列表：[{ id, text, completed }]
  const todos = ref([]);

  // 添加任务
  const addTodo = (text) => {
    todos.value.push({
      id: Date.now(),
      text: text.trim(),
      completed: false
    });
  };

  // 切换任务完成状态
  const toggleTodo = (id) => {
    const todo = todos.value.find(t => t.id === id);
    if (todo) todo.completed = !todo.completed;
  };

  // 删除任务
  const deleteTodo = (id) => {
    todos.value = todos.value.filter(t => t.id !== id);
  };

  // 持久化：保存到 localStorage
  const saveToLocal = () => {
    localStorage.setItem('todos', JSON.stringify(todos.value));
  };

  // 从 localStorage 加载
  const loadFromLocal = () => {
    const saved = localStorage.getItem('todos');
    if (saved) todos.value = JSON.parse(saved);
  };

  return { todos, addTodo, toggleTodo, deleteTodo, saveToLocal, loadFromLocal };
});
步骤 3：TodoItem 组件（src/components/TodoItem.vue）
vue
<template>
  <li :class="{ completed: todo.completed }">
    <input
      type="checkbox"
      v-model="todo.completed"
      @change="$emit('toggle', todo.id)"
    />
    <span>{{ todo.text }}</span>
    <button @click="$emit('delete', todo.id)">删除</button>
  </li>
</template>

<script setup>
// 接收父组件传递的任务数据
defineProps(['todo']);
// 声明派发的事件
defineEmits(['toggle', 'delete']);
</script>

<style scoped>
.completed {
  text-decoration: line-through;
  color: #999;
}
li {
  display: flex;
  align-items: center;
  gap: 10px;
  margin: 8px 0;
}
</style>
步骤 4：Home 视图（src/views/Home.vue）
vue
<template>
  <div class="todo-container">
    <h1>我的 Todo 列表</h1>
    <!-- 添加任务输入框 -->
    <input
      v-model="newTodo"
      @keyup.enter="addTodo"
      placeholder="请输入任务内容，按回车添加"
      class="todo-input"
    />
    <!-- 任务列表 -->
    <ul class="todo-list">
      <TodoItem
        v-for="todo in store.todos"
        :key="todo.id"
        :todo="todo"
        @toggle="handleToggle"
        @delete="handleDelete"
      />
    </ul>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useTodosStore } from '../stores/todos';
import TodoItem from '../components/TodoItem.vue';

// 获取 Store 实例
const store = useTodosStore();
// 新任务输入框绑定值
const newTodo = ref('');

// 添加任务
const addTodo = () => {
  if (newTodo.value.trim()) {
    store.addTodo(newTodo.value);
    store.saveToLocal(); // 保存到本地
    newTodo.value = ''; // 清空输入框
  }
};

// 切换任务状态
const handleToggle = (id) => {
  store.toggleTodo(id);
  store.saveToLocal();
};

// 删除任务
const handleDelete = (id) => {
  store.deleteTodo(id);
  store.saveToLocal();
};

// 初始化加载本地数据
store.loadFromLocal();
</script>

<style scoped>
.todo-container {
  max-width: 600px;
  margin: 20px auto;
  padding: 0 20px;
}
.todo-input {
  width: 100%;
  padding: 8px;
  font-size: 16px;
  margin-bottom: 10px;
}
.todo-list {
  list-style: none;
  padding: 0;
}
</style>
步骤 5：路由配置（src/router/index.js）
javascript
运行
import { createRouter, createWebHistory } from 'vue-router';
import Home from '../views/Home.vue';

const routes = [
  { path: '/', component: Home }
];

const router = createRouter({
  history: createWebHistory(),
  routes
});

export default router;
步骤 6：全局配置（src/main.js）
javascript
运行
import { createApp } from 'vue';
import { createPinia } from 'pinia';
import App from './App.vue';
import router from './router';

const app = createApp(App);
app.use(createPinia());
app.use(router);
app.mount('#app');
步骤 7：根组件（App.vue）
vue
<template>
  <router-view />
</template>

<script setup>
import { onMounted } from 'vue';
import { useTodosStore } from './stores/todos';

// 页面卸载前自动保存数据
onMounted(() => {
  const store = useTodosStore();
  window.addEventListener('beforeunload', () => {
    store.saveToLocal();
  });
});
</script>
步骤 8：运行与扩展
bash
运行
# 启动开发服务
npm run dev
扩展功能建议
添加任务编辑功能（双击任务文本进入编辑模式）
新增详情页路由 /todo/:id，展示单个任务详情
添加任务过滤（全部 / 已完成 / 未完成）
集成 Axios 对接后端 API（替换 localStorage）
部署到 Vercel/Netlify
六、进阶学习方向
TypeScript 集成：重新创建项目时选择 TypeScript，为代码添加类型约束，提升可维护性。
单元测试：使用 Vitest 编写组件 / Store 单元测试，保障代码质量。
UI 库集成：学习 Element Plus/Ant Design Vue 等 UI 库，快速构建美观的界面。
真实项目实战：开发博客系统、电商商品列表等项目，托管到 GitHub 并部署上线。
总结
通过本教程的四个阶段学习，你已掌握 Vue 3 核心语法、组件化开发、路由 / 状态管理等关键技能，能够独立开发中型 Vue 应用。后续需通过持续实践（如仿写开源项目、参与实际开发）巩固知识，逐步提升开发能力。
勤加练习，坚持实践，你也能成为优秀的 Vue 开发者！
