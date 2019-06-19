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
        <v-subheader>Идеи</v-subheader>
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
      <v-list subheader>
        <v-subheader>В поисках интересных комбинаций</v-subheader>

        <v-list-tile v-for="(todo, index) in filtredTodo" :key="index">
          <v-list-tile-content>
            <v-list-tile-title>{{ todo }}</v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>

        <v-divider/>
      </v-list>
    </v-content>
  </v-app>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      drawer: true,
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
      let concated = [];
      this.todosChecked.forEach(first => {
        concated.push(first);
        this.todosChecked.forEach(second => {
          if (first !== second) {
            if (!concated.includes(second + " + " + first)) {
              concated.push(first + " + " + second);
            }
          }
        });
      });
      return concated;
    }
  },
  methods: {
    addTodo() {
      this.todos.push(this.newItem);
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
    }
  }
};
</script>
