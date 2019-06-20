<template>
  <v-app>
    <v-toolbar color="primary" app dark clipped-left>
      <v-toolbar-side-icon></v-toolbar-side-icon>

      <v-toolbar-title class="headline text-uppercase">
        <span>ГЕНЕРАТОР ИДЕЙ</span>
      </v-toolbar-title>
    </v-toolbar>

    <v-navigation-drawer app clipped v-model="drawer" permanent>
      <v-text-field
        type="text"
        label="Добавить ещё идею"
        v-model="newItem"
        box
        append-icon="send"
        @click:append="addTodo"
      ></v-text-field>

      <v-list subheader>
        <v-subheader>Идеи. Использовать следующие:</v-subheader>
        <v-list-tile v-for="(todo, index) in todos" :key="index">
          <v-list-tile-action>
            <v-checkbox
              :value="todo"
              :label="todo"
              v-model="todosChecked"
              @change="saveInLocaleStorage"
            ></v-checkbox>
          </v-list-tile-action>
          <v-spacer></v-spacer>
          <v-list-tile-action>
            <v-btn icon @click="removeTodo(index)">
              <v-icon>clear</v-icon>
            </v-btn>
          </v-list-tile-action>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>

    <v-content>
      <template>
        <v-expansion-panel expand v-model="panel" popout class="pa-4">
          <template v-for="(todo, index) in filtredTodo">
            <v-expansion-panel-content v-bind:key="index" class="blue lighten-4">
              <template v-slot:header>
                <div>Элементов: {{index + 1}}</div>
              </template>

              <v-card>
                <v-card-text
                  class="grey lighten-3"
                  v-for="(subtodo, subindex) in todo"
                  v-bind:key="index+ '-'+subindex"
                >{{ subtodo.join(" + ")}}</v-card-text>
              </v-card>
            </v-expansion-panel-content>
          </template>
        </v-expansion-panel>
      </template>
    </v-content>
  </v-app>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      drawer: true,
      panel: [false, true, true, true],
      newItem: "",
      todos: localStorage.getItem("todos")
        ? JSON.parse(localStorage.getItem("todos"))
        : ["Инструменты", "Фильтры", "Нейросети"],
      todosChecked: localStorage.getItem("todosChecked")
        ? JSON.parse(localStorage.getItem("todosChecked"))
        : []
    };
  },
  computed: {
    filtredTodo() {
      let temp = [];
      for (let step = 0; step < this.todosChecked.length; step++) {
        temp.push(this.permute(this.todosChecked, step));
      }
      return temp;
    }
  },
  methods: {
    addTodo() {
      this.todos.push(this.newItem);
      this.todosChecked.push(this.newItem);
      this.newItem = "";
      this.saveInLocaleStorage();
    },
    removeTodo(index) {
      if (this.todosChecked.includes(this.todos[index])) {
        this.todosChecked.splice(
          this.todosChecked.indexOf(this.todos[index]),
          1
        );
      }
      this.todos.splice(index, 1);
      this.saveInLocaleStorage();
    },
    saveInLocaleStorage() {
      localStorage.setItem("todos", JSON.stringify(this.todos));
      localStorage.setItem("todosChecked", JSON.stringify(this.todosChecked));
    },
    permute(args, maxElems) {
      var result = [];
      maxElems--;
      if (maxElems < 0) {
        for (let s of args) {
          result.push([s]);
        }
        return result;
      }

      for (var i = 0; i < args.length - maxElems; i++) {
        for (var j = i + 1; j + maxElems < args.length; j++) {
          var temp = [];
          temp.push(args[i]);
          temp.push(args[j]);
          for (var k = 1; k <= maxElems; k++) {
            temp.push(args[j + k]);
          }
          result.push(temp);
        }
      }
      return result;
    }
  }
};
</script>
