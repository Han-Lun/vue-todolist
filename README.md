# vue-todolist

基于Vue的TodoList示例，麻雀虽小，五脏俱全

## 技术栈

Vue + localStorage + hash

## 功能描述(使用说明)

- 添加备忘录(输入标题后回车添加,如果内容为空或只有空格会清空，什么都不添加)
- 删除备忘录(点击标题后面的叉)
- 完成备忘录(点击标题前面的复选框)
- 编辑备忘录(双击标题进入编辑模式)
- 取消编辑备忘录(按ESC或者失去焦点时)
- 完成编辑备忘录(按回车键完成[如果内容为空的时候会自动删除]，此时也会调用到失去焦点事件)
- 一键完成所有备忘录(点击第一行的复选框)
- 过滤任务，显示全部，未完成，已完成的备忘录(点击全部，未完成，已完成按钮)
- 清空已完成备忘录(点击清空已完成)
## Vue相关知识点练习：
- 加载环境
> npm init -y  初始化package.json
npm i -S underscore vue todomvc-app-css 装载环境
vim index.html
复制格式化后的html
引入css
将英文标题换成中文标题
引入vue.js
新建vue的实例
写一个默认的任务：todoList: [{}]
- el选项挂载DOM
```
  // 新建一个Vue的实例对象
  var todoapp = new Vue({
    // 挂载
    el: '.todoapp',
    // 数据
    data: {
         // 备忘录数组
      todoList: [
        // 一个任务就是一个对象，text表示任务的名称，checked为true表示已完成，false表示未完成
        {
          text: '学Vue',
          checked: false
        },
        {
          text: '学React',
          checked: false
        }
      ]
    },
    方法
    methods: {

    },
    // 计算属性
    computed: {

    }
  })

```
- 属性
```
data: {
      newTodo: '',
      todos: todoStorage.fetch(),
      editedTodo: null,
      beforeEditCache: '',
      visibility
    }
```
- 计算属性（get,set）
```
computed: {
      showTodos() {
        return this.todos.length > 0
      },
      activeCount() {
        return filters.active(this.todos).length
      },
      completedCount() {
        return filters.completed(this.todos).length
      },
      allDone: {
        get() {
          return this.activeCount === 0
        },
        set(value) {
          this.todos.map(todo => {
            todo.completed = value
          })
        }
      },
      filteredTodos() {
        return filters[this.visibility](this.todos)
      }
    }
  ```
```
(function(){
  var STORAGE_KEY = 'todos'
  window.todoStorage = {
    fetch() {
      try {
        return JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]')
      } catch(err) {
        return [];
      }
    },
    save(todos) {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(todos))
    }
  }
})();
```
- 属性观察
```
 var filters = {
    all: todos => todos,
    active: todos => todos.filter(todo => !todo.completed),
    completed: todos => todos.filter(todo => todo.completed)
  }
  var visibility = location.hash.substr(location.hash.indexOf('/')+1)
  visibility = visibility === '' ? 'all' : visibility

watch: {
      todos: {
        deep: true,
        handler: todoStorage.save
      }
    }
```
- 方法
```
methods: {
      addTodo() {
        this.newTodo = this.newTodo.trim()
        if (!this.newTodo) {
          return
        }
        this.todos.unshift({
          title: this.newTodo,
          completed: false
        })
        this.newTodo = ''
      },
      removeTodo(todo) {
        var index = this.todos.indexOf(todo)
        this.todos.splice(index, 1)
      },
      editTodo(todo) {
        this.editedTodo = todo
        this.beforeEditCache = todo.title
      },
      doneEdit(todo) {
        if (!this.editedTodo) {
          return;
        }
        this.editedTodo = null;
        todo.title = todo.title.trim()
        if (!todo.title) {
          this.removeTodo(todo)
        }
      },
      cancelEdit(todo) {
        if (this.editedTodo) {
          todo.title = this.beforeEditCache
          this.editedTodo = null
        }
      },
      removeCompleted() {
        this.todos = filters.active(this.todos)
      }
    }
 ```
- 指令
```
 directives: {
      focus: {
        update(el) {
          el.focus()
        }
      }
    }
```