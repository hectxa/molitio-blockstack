<template>
  <div class="dashboard">
    <div class="container">
      <div class="row justify-content-md-center">
        <div class="col-md-8 col-md-offset-2">
          <img src="/logoTop.png" class="logo-top">
          <h1 class="page-header align-middle">Molitio
            <img :src="user.avatarUrl() ? user.avatarUrl() : '/avatar-placeholder.png'" class="avatar">
            <small><span class="sign-out">(<a href="#" @click.prevent="signOut">Sign Out</a>)</span></small>
          </h1>
          <h2 class="user-info">
            <small>
              My Tasks
            </small>
            <small class="float-right">
            {{ user.username ? user.username : user.identityAddress }}
            </small>
          </h2>
          <form @submit.prevent="addTodo" :disabled="! todo">
            <div class="input-group">
              <input v-model="todo" type="text" class="form-control" placeholder="Enter Task Name..." autofocus>
              <input v-model="superseconds" type="date" class="form-control2" placeholder="m/d/y" autofocus>
              <span class="input-group-btn">
                <button class="roundbtn btn btn-default" type="submit" :disabled="! todo ">+</button>
              </span>
            </div>
          </form>

          <ul class="list-group">
            <li v-for="todo in todos"
              class="list-group-item"
              :class="{completed: todo.completed}"
              :key="todo.id">
              <label>
                <input type="checkbox" v-model="todo.completed">{{ todo.text }}
              </label>
              <p class="daysleft-label">days left</p>
              <p class="day-label">{{todo.days}}</p>

              <a @click.prevent="todos.splice(todos.indexOf(todo), 1)"
                class="delete float-right"
                href="#">x</a>

              <p class="cell-date">{{todo.superseconds}}</p>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { userSession } from '../userSession'
var STORAGE_FILE = 'todos.json'
var today = new Date();

export default {
  name: 'dashboard',
  props: ['user'],
  data () {
    return {
      todos: [],
      todo: '',
      superseconds: 0,
      actualseconds: 0,
      uidCount: 0,
      date: 0,
      days: 0
    }
  },
  watch: {
    todos: {
      handler: function (todos) {
        // encryption is now enabled by default
        return userSession.putFile(STORAGE_FILE, JSON.stringify(todos))
      },
      deep: true
    }
  },
  mounted () {
    this.fetchData()
  },
  methods: {
    addTodo () {
      if (!this.todo.trim()) {
        return
      }
      var monthNames = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'June', 'July', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
      var datesplit = this.superseconds.split('-');
      var y = datesplit[0]
      var m = datesplit[1] - 1
      var d = datesplit[2]
      var selDate = new Date(y, m, d)
      var secondsLeft = (selDate.getTime() - today.getTime()) / 1000

      var minleft = secondsLeft / 60
      var hoursleft = minleft / 60
      var daysleft = hoursleft / 24


      this.todos.unshift({
        id: this.uidCount++,
        text: this.todo.trim(),
        superseconds: monthNames[m] + ' ' + d + ', ' + y,
        actualseconds: secondsLeft,
        completed: false,
        date: selDate,
        days: Math.round(daysleft)
      })

      this.todo = ''
    },

    fetchData () {
      userSession.getFile(STORAGE_FILE) // decryption is enabled by default
        .then((todosText) => {
          var todos = JSON.parse(todosText || '[]')

          todos.forEach(function (todo, index) {
            todo.id = index
            let seldat = todo.date.substring(0, 10)
            var datesplit = seldat.split('-')
            var y = datesplit[0]
            var m = datesplit[1] - 1
            var d = datesplit[2]
            var selDate = new Date(y, m, d)
            var secondsLeft = (selDate.getTime() - today.getTime()) / 1000

            var minleft = secondsLeft / 60
            var hoursleft = minleft / 60
            var daysleft = Math.round(hoursleft / 24)

            todo.days = daysleft
          })

          todos.sort(function (a, b) {
            return a.days - b.days;
          });

          this.uidCount = todos.length
          this.todos = todos

        })
    },

    signOut () {
      userSession.signUserOut(window.location.href)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
  $font-stack: Helvetica, sans-serif;
.dashboard {
  padding-top: 2rem;
  color: rgba(250, 255, 255, 0.98);
  font: 100% $font-stack;
}
.input-group {
  background-color: #0b0c0c;
}
  .form-control {
    background-color: #0b0c0c;
    border: none;
    color: white;
  }

  .form-control2 {
    background-color: #0b0c0c;
    border: none;
    color: lightgrey;
    cursor: pointer;
  }
.roundbtn {
  border-radius: 10px;
  background-color: #252729;
}
.list-group {
  margin-top: 5px;
}
input::placeholder {
  color: darkgray;
}
.form-control:focus {
  border: none;
}
.form-control2:focus {
  border: none;
}

label {
  margin-bottom: 0;
  // width: 100%;
  cursor: pointer;
  input[type="checkbox"] {
    margin-right: 5px;
  }
}
.list-group-item {
  margin: 2px;
  background-color: #1a1d1f;
  height: 60px;
  border-radius: 10px;
  &.completed label {
    text-decoration: line-through;
    text-decoration-color: #00fffe;
  }
  .day-label {
    position: absolute;
    font-size: 25px;
    top: 15px;
    float: right;
    color: #00fffe;
    right: 4%;
  }

  .daysleft-label {
    position: absolute;
    top: 15px;
    right: 4%;
    float: right;
    font-size: 10px;
    padding-top: 25px;

  }

  .cell-date {
    color: rgba(147, 147, 147, 0.98);
    padding-top: 5px;
  }

  .delete {
    display: none;
    position: absolute;
    right: 2%;
    top: 2%;
  }

  &:hover .delete {
    display: inline;
    color: white;
    &:hover {
      text-decoration: none;
      color: #c90000;
    }
  }
}
</style>
