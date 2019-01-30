<template>
  <div class="container">
    <h1 class="title has-text-left">List Todo</h1>
    <h2 class="subtitle has-text-left">View all todo</h2>
    <div class="columns">
      <div class="column tile is-ancestor">
        <div class="tile is-vertical">
          <transition-group name="list" tag="div">
            <div
              v-for="(todo) in data"
              :key="todo.id"
              class="tile box"
              v-if="todo.completed === false"
            >
              <div class="tile">
                <p class="is-pulled-left">{{todo.tittle}}</p>
              </div>
              <a
                class="button is-primary is-pulled-right"
                style="margin-right:10px;"
                @click="completeTodo()"
              >
                <span class="icon is-small">
                  <i class="fas fa-check"></i>
                </span>
              </a>
              <!-- <a class="button is-danger is-pulled-right">
                <span class="icon is-small">
                  <i class="fas fa-trash"></i>
                </span>
              </a>-->
            </div>
          </transition-group>
        </div>
      </div>
      <div class="column">
        <b-field label="Title">
          <b-input v-model="selected.tittle" :disabled="!enableControl"></b-input>
        </b-field>

        <!-- <b-field label="Completed">
            <b-select placeholder="Is todo completed ?" v-model="selected.completed" :disabled="!enableControl" >
                <option :value=true >Yes</option>
                <option :value=false >No</option>
            </b-select>
        </b-field>-->
        <a class="button is-primary" @click="createTodo()" :disabled="isAdd">Tambah</a>
        <a class="button is-info" @click="saveTodo()" :disabled="!isAdd ">Simpan</a>
        <div>{{selected}}</div>
        <!-- <a class="button is-warning" @click="editTodo()" :disabled="this.selected != {}">Ubah</a> -->
        <!--  -->
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      isEdit: false,
      isAdd: false,
      enableControl: false,
      selected: {},
      data: [],
      columns: [
        {
          field: 'id',
          label: 'ID',
          width: '40',
          numeric: true
        },
        {
          field: 'tittle',
          label: 'Title'
        },
        {
          field: 'createdAt',
          label: 'Date',
          centered: true
        },
        {
          field: 'completed',
          label: 'Completed'
        }
      ]
    }
  },
  created () {
    this.fetchData()
  },
  methods: {
    fetchData: function () {
      axios
        .get('http://spring-todoapp.herokuapp.com/api/todos')
        .then(response => {
          this.data = response.data.content
        })
        .catch(error => {
          console.log(error)
        })
    },
    createTodo: function () {
      this.selected = {}
      this.enableControl = true
      this.isAdd = true
    },
    editTodo: function () {
      this.enableControl = true
      this.isEdit = true
    },
    saveTodo: function () {
      this.selected.completed = false
      axios
        .post('http://spring-todoapp.herokuapp.com/api/todos', this.selected)
        .then(response => {
          this.selected = {}
          this.enableControl = false
          this.isEdit = false
          this.isAdd = false
          this.fetchData()
        })
        .catch(error => {
          console.log(error)
        })
    },
    completeTodo: function (todo) {
      todo.completed = true
      axios
        .put('http://spring-todoapp.herokuapp.com/api/todos/' + todo.id, todo)
        .then(response => {
          this.selected = {}
          this.enableControl = false
          this.isEdit = false
          this.isAdd = false
          this.fetchData()
        })
        .catch(error => {
          console.log(error)
        })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.list-enter-active,
.list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to /* .list-leave-active below version 2.1.8 */ {
  opacity: 0;
  transform: translateY(45px);
}
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
