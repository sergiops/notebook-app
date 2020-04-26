<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <h1>Notebook</h1>

    <!-- Main pane --> 
    <section class="main">
      <textarea placeholder="Write here" v-model="content"></textarea>
    </section>

    <!-- Preview pane -->
    <aside class="preview" v-html="notePreview">
    </aside>
  </div>
</template>

<script>
import marked from "marked"

export default {
  name: 'App',
  computed: {
    notePreview () {
      return marked(this.content)
    }
  },
  data () {
    return {
      content: ""
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.main {
  background-color: #eee;
}

.preview {
  background-color: #ddd;
}
</style>
