<template>
  <div id="app">
    
  <section id="main">
  <!-- Main app -->
      <div class="row no-gutters" style="height: 100%;">
        <div class="col-2">
          <div class="aside-wrapper">
            <h3>Notebook</h3>
            <button @click="addNote">Add Note</button>
            <div>
              <div v-for="note of notes" :key="note.title">
                {{note.title}}
              </div>
            </div>
          </div>
        </div>
        <div class="col-5">
          <!-- Text input pane --> 
          <section class="textarea-wrapper">
          <textarea placeholder="Write here" v-model="content"></textarea>
          </section>
        </div>
        <div class="col-5">
          <!-- Preview pane -->
          <section class="preview" v-html="notePreview">
          </section>
        </div>

      </div>
    </section>
  </div>
</template>

<script>
import marked from "marked"

export default {
  name: 'App',
  methods: {
    addNote() {
      const time = Date.now()
      const note = {
        id: String(time),
        title: "New Note",
        content: "**Hi!**",
        created: time,
        favorite: false
      }
      this.notes.push(note)
    }
  },
  computed: {
    notePreview () {
      return marked(this.content)
    }
  },
  watch: {
    content(val) {
      localStorage.setItem("content", val)
    }
  },
  created() {
    this.content = localStorage.getItem("content") || ""
  },
  data () {
    return {
      content: "",
      notes: []
    }
  }
}
</script>

<style>
#app {
  display: flex;
  flex-flow: column;
  height: 100vh;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

#main {
  height: 100%;
  overflow-y: scroll;
}

.textarea-wrapper {
  width: 100%;
  height: 100%;
  padding: 20px;
  background-color: aqua;
}

.preview {
  background-color: #ddd;
  width: 100%;
  height: 100%;
  padding: 20px;
  overflow-y: scroll;
}

textarea {
  height: 100%;
  width: 100%;
  border: none;
  resize: none !important;
  outline: none;
}

.aside-wrapper {
  padding: 10px;
}
</style>
