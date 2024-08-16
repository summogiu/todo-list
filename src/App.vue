<template>
  <div class="frame">
    <div class="start-anima-box">
      <h1>TODO LIST</h1>
      <p>good friend in life</p>
    </div>
    <div class="main-box">
      <div class="time-box">
        <div class="date-box">
          <p class="year-p">{{ allTime.year }}</p>
          <p><span class="month-p">{{ allTime.month }}</span> / <span class="date-p">{{ allTime.day }}</span></p>
        </div>
        <p class="time-p">{{ `${allTime.hours}:${allTime.minutes}` }}</p>
      </div>
      <div class="input-box">
        <input type="text" class="add-input" v-model="newTodo" @keyup.enter="addTodo">
        <button type="button" class="add-btn" @click="addTodo">add</button>
      </div>
      <div class="list-box" :class="[isUlAnima ? 'in' : '']">
        <ul class="list-ul">
          <li v-for="item, i in filteredTodos" :key="i">
            <div class="not-editing" v-if="item !== editingTodo">
              <input type="checkbox" class="list-checkbox" v-model="item.completed">
              <p class="list-content" :class="[item.completed ? 'isCompleted' : '']" @dblclick="editTodo(item)" @touchstart="editTodo(item)">{{ item.content }}</p>
              <button type="button" class="delete-btn" @click="removeTodo(item)"></button>
            </div>
            <input type="text" class="edit-input"
                  :class="[item === editingTodo? 'show' : '']"
                  v-focus-todo="editingTodo === item"
                  v-model="item.content"
                  @keyup.enter="doneEdit(item)"
                  @blur="doneEdit(item)"
                  @keyup.esc="cancelEdit(item)">
          </li>
        </ul>
        <p class="no-content-message" v-if="filteredTodos.length === 0">no content</p>
      </div>
      <div class="other-box">
        <p class="remaining-num">remaining: {{ remaining }} {{ unit }}</p>
        <div class="other-box-btn-box">
          <button type="button" class="other-box-btn" v-if="type === 'all' " @click="selectAll">select all</button>
          <button type="button" class="other-box-btn" @click="clearCompleted">clear completed</button>
        </div>
      </div>
      <div class="type-btn-box">
        <button type="button" class="type-btn" :class="[type === 'all' ? 'active' : '']" @click="type = 'all'">all</button>
        <button type="button" class="type-btn" :class="[type === 'active' ? 'active' : '']" @click="type = 'active'">active</button>
        <button type="button" class="type-btn" :class="[type === 'completed' ? 'active' : '']" @click="type = 'completed'">completed</button>
      </div>
    </div>
  </div>
</template>

<style lang="scss">
.frame{
  height: 100%;
  min-height: 100vh;
  width: 100%;
  overflow-y: auto;
  background: linear-gradient(120deg, rgb(228, 177, 192),rgb(224, 186, 198), rgb(255, 255, 255));
  font-family:'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
  color: rgb(70, 70, 70);
}
.start-anima-box{
  height: 100%;
  width: 100%;
  position: fixed;
  z-index: 20;
  background: linear-gradient(120deg, rgb(228, 177, 192),rgb(224, 186, 198), rgb(255, 255, 255));
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  animation: fadeOut .5s 1.5s forwards;

  h1{
    font-size: 5rem;
    color: white;
    opacity: 0;
    transform: translateY(-10%);
    animation: fadeIn .5s forwards;
  }
  p{
    font-size: 1.5rem;
    color: white;
    letter-spacing: 5px;
    opacity: 0;
    animation: fadeIn .5s .3s forwards;
  }
}

.main-box{
  position: relative;
  top: 20%;
  width: 500px;
  margin: 50px auto;
  border-radius: 15px;
  background: rgba(255, 255, 255, 0.8);
  box-shadow: 0px 1px 5px rgb(199, 162, 177);
  overflow: hidden;
}
@media(max-width: 500px){
  .main-box{
    width: 100%;
    min-height: 100vh;
    margin: 0;
    border-radius: 0;
  }
}

.time-box{
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  line-height: 1;
  padding: 20px 20px 0;

  .date-box{
    .year-p{
      color: rgb(204, 83, 120);
      font-family:'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
      font-weight: bold;
      font-size: 2rem;
    }
    p{
      color: rgb(204, 83, 120);
      font-family: 'Times New Roman', Times, serif;
      font-size: 2rem;
    }
    .month-p {
      color: rgb(241, 163, 187);
      font-weight: bold;
      font-size: 5rem;
    }
    .date-p {
      color: rgb(228, 68, 116);
      font-weight: bold;
      font-size: 5rem;
    }
  }
  .time-p{
    font-size: 6rem;
    font-family:'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
  }
}
@media(max-width: 500px){
  .time-box{
    margin-top: 10px;
    .date-box{
      white-space: nowrap;
    }
    .time-p{
      display: none;
    }
  }
}

.input-box{
  margin-top: 20px;
  padding: 0 20px;
  display: flex;
  justify-content: space-between;

  .add-input{
    background: transparent;
    outline: none;
    border: 1px solid rgb(206, 206, 206);
    border-radius: 25px;
    width: 100%;
    padding: 5px 10px;
    margin-right: 20px;
    font-size: 1.2rem;
    color: rgb(70, 70, 70);
  }
  .add-btn{
    background: rgb(226, 120, 152);
    outline: none;
    border: none;
    border-radius: 25px;
    padding: 5px 10px;
    color: white;
    font-size: 1rem;
    cursor: pointer;
    box-shadow: 1px 1px 5px rgb(229, 151, 175);
    transition:  scale .5s, box-shadow .5s, opacity .5s;

    &:hover{
      scale: 1.1;
      opacity: 0.8;
    }
  }
}
@media(max-width: 500px){
  .input-box{
    .add-btn{
      &:hover{
        scale: 1;
        opacity: 1;
      }
    }
  }
}

.list-box{
  padding: 0 20px;

  .list-ul{
    li{
      padding: 10px 0;

      .not-editing{
        position: relative;
        display: flex;
        align-items: center;

        .list-checkbox{
          appearance: none;
          position: relative;
          width: 30px;
          height: 30px;
          cursor: pointer;

          &::before{
            position: absolute;
            top: 0;
            left: 0;
            content: '';
            width: 30px;
            height: 30px;
            border-radius: 50%;
            border: 1px solid rgb(206, 206, 206);
            transition: scale .3s;
          }
          &::after{
            position: absolute;
            top: 5px;
            left: 10px;
            content: '';
            width: 10px;
            height: 20px;
            border: solid rgb(226, 120, 152);
            border-width: 0 2px 2px 0;
            transform: rotate(45deg);
            scale: 0;
            transition: scale .3s;
          }
          &:checked{
            &::before{
              scale: 0;
            }
            &::after{
              scale: 1;
            }
          }
        }
        .list-content{
          font-size: 1.2rem;
          margin-left: 10px;
          width: 100%;
          transition: all .5s;
        }
        .list-content.isCompleted{
          color: rgb(167, 167, 167);
          text-decoration: line-through;
        }
        .delete-btn{
          position: absolute;
          top: 50%;
          right: 0;
          transform: translateY(-50%);
          width: 30px;
          height: 30px;
          border: none;
          background: transparent;
          display: none;
          cursor: pointer;

          &::before,&::after{
            content: "";
            position: absolute;
            width: 2px;
            height: 18px;
            background-color: rgb(194, 35, 75);
            top: 7px;
            left: 12px;
          }
          &::before {
            transform: rotate(45deg);
          }
          &::after {
            transform: rotate(-45deg);
          }
        }
        &:hover{
          .delete-btn{
            display: block;
          }
        }
      }
      .edit-input{
        background: transparent;
        outline: none;
        border: 1px solid rgb(206, 206, 206);
        border-radius: 25px;
        width: calc(100% - 36px);
        padding: 5px 10px;
        margin-left: 36px;
        color: rgb(70, 70, 70);
        font-family: inherit;
        font-size: 1.2rem;
        display: none;
      }
      .edit-input.show{
        display: block;
      }
    }
  }
  .no-content-message{
    text-align: center;
    padding: 20px 0;
    color: rgb(197, 197, 197);
    font-size: 1.2rem;
  }
}
.list-box.in{
  animation: fadeIn .5s forwards;
}
@media(max-width: 500px){
  .list-box{
    padding: 20px;
    .list-ul{
      max-height: 60vh;
      overflow-y: auto;

      &::-webkit-scrollbar{
        width: 0;
        height: 0;
      }

      li{
        .not-editing{
          .delete-btn{
            display: block;

            &::before,&::after{
              background-color: rgb(241, 163, 187);
            }
          }
        }
      }
    }
  }

  @media(min-height: 700px) {
    .list-box{
      .list-ul{
        max-height: 70vh;
      }
    }
  }
}

.other-box{
  display: flex;
  justify-content: space-between;
  padding: 5px 10px 5px 20px;

  .remaining-num{
    color: rgb(241, 163, 187);
    white-space: nowrap;
  }
  .other-box-btn-box{
    .other-box-btn{
      margin-left: 5px;
      background: white;
      border: 1px solid rgb(241, 163, 187);
      border-radius: 5px;
      color: rgb(241, 163, 187);
      cursor: pointer;

      &:hover{
        background: rgb(255, 235, 242);
      }
    }
  }
}
@media(max-width: 500px){
  .other-box{
    position: fixed;
    top: 0;
    right: 5px;
    width: 100%;
    flex-direction: column;
    align-items: flex-end;

    .other-box-btn-box{
      .other-box-btn{
        &:hover{
          background: white;
        }
      }
    }
  }
}

.type-btn-box{
  display: flex;

  .type-btn{
    border: none;
    background: linear-gradient(120deg, rgb(240, 193, 207), rgb(245, 206, 219));
    color: white;
    padding: 10px;
    width: 100%;
    font-size: 1.2rem;
    font-family:'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
    cursor: pointer;
  }
  .type-btn.active{
    background: transparent;
    color: rgb(226, 120, 152);
    cursor: default;
  }
}
@media(max-width: 500px){
  .type-btn-box{
    position: absolute;
    bottom: 0;
    left: 0;

    .type-btn{
      width: calc(100vw / 3);
    }
  }
}

@keyframes fadeIn {
  0% {
    opacity: 0;
    transform: translate(0, -10%);
  }
  100% {
    opacity: 1;
    transform: translate(0, 0);
  }
}
@keyframes fadeOut {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
    display: none;
  }
}
</style>

<script>
import { ref, watch, computed, onMounted } from 'vue'

const STORAGE_KEY = 'giugiu-todolist-1.0'
const todoStorage = {
  fetch () {
    const todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]')
    todos.forEach((item, i) => {
      item.id = i
    })
    todoStorage.uid = todos.length // 在新增內容時可保持最新id資訊
    return todos
  },
  save (todos) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos))
  }
}

const filters = {
  all: (todos) => todos,
  active: (todos) => todos.filter(item => !item.completed),
  completed: (todos) => todos.filter(item => item.completed)
}

export default {
  setup () {
    const todos = ref([])
    const type = ref('all')
    const filteredTodos = computed(() => {
      return filters[type.value](todos.value)
    })

    const newTodo = ref('')
    const addTodo = () => {
      if (!newTodo.value) { return }
      const value = newTodo.value && newTodo.value.trim()
      todos.value.push({
        id: todoStorage.uid,
        content: value,
        completed: false
      })
      newTodo.value = ''
    }

    const removeTodo = (item) => {
      todos.value.splice(todos.value.indexOf(item), 1)
    }
    const editingTodo = ref(null)
    // eslint-disable-next-line no-unused-vars
    let brforeEditContent = ''
    const editTodo = (item) => {
      editingTodo.value = item
      brforeEditContent = item.content
    }
    const doneEdit = (item) => {
      if (!editingTodo.value) { return }
      editingTodo.value = null
      item.content = item.content.trim()
      if (!item.content) { removeTodo(item) }
    }
    const cancelEdit = (item) => {
      editingTodo.value = null
      item.content = brforeEditContent
    }

    const remaining = computed(() => {
      return filters.active(todos.value).length
    })
    const unit = computed(() => {
      return remaining.value === 1 ? 'item' : 'items'
    })

    const selectAll = () => {
      const value = remaining.value === 0
      todos.value.forEach(item => {
        item.completed = !value
      })
    }
    const clearCompleted = () => {
      todos.value = filters.active(todos.value)
    }

    watch(todos, () => {
      todoStorage.save(todos.value)
    }, { deep: true })

    onMounted(() => {
      todos.value = todoStorage.fetch()
    })

    // 時間
    const allTime = ref({})
    const updateTime = () => {
      const now = new Date()
      allTime.value.year = now.getFullYear()
      allTime.value.month = now.getMonth() + 1
      allTime.value.day = now.getDate()
      allTime.value.hours = String(now.getHours()).padStart(2, '0')
      allTime.value.minutes = String(now.getMinutes()).padStart(2, '0')
      setTimeout(updateTime, 1000)
    }

    updateTime()

    // 動畫
    const isUlAnima = ref(false)
    const triggerAnima = () => {
      isUlAnima.value = !isUlAnima.value
    }
    watch(type, () => {
      triggerAnima()
      setTimeout(triggerAnima, 500)
    }, { deep: true })

    return {
      type,
      filteredTodos,

      newTodo,
      addTodo,
      removeTodo,

      editingTodo,
      editTodo,
      doneEdit,
      cancelEdit,

      remaining,
      unit,
      selectAll,
      clearCompleted,

      allTime,

      isUlAnima
    }
  },
  directives: {
    'focus-todo': {
      updated (el, binding) {
        if (binding.value) {
          el.focus()
        }
      }
    }
  }
}
</script>
