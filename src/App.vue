<template>
  <div id="app"> 
  <!-- Main app -->

    <nav id="nav-bar">
      <div class="nav-action text-center">
        <h3>Notebook</h3>
        <button @click="addNote">Add Note</button>
      </div>
      <hr class="nav-divider"/>
      <div>
        <div class="nav-note" 
        v-for="note of notes" :key="note.id"
        @click="selectNote(note)">
          {{note.title}}
        </div>
      </div>
    </nav>
  
    <section id="note-content" class="row no-gutters">
      <div class="col-6">
        <!-- Text input pane --> 
        <section class="textarea-wrapper">
          <textarea placeholder="Write here" v-model="selectedNote.content"></textarea>
        </section>
      </div>
      <div class="col-6 preview-bg">
        <!-- Preview pane -->
        <section class="preview" v-html="notePreview"></section>
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
        content: "",
        created: time,
        favorite: false
      }
      this.notes.push(note)
      this.selectedId = note.id
    },
    selectNote(note) {
      this.selectedId = note.id
    }
  },
  computed: {
    notePreview () {
      return this.selectedNote ? marked(this.selectedNote.content) : ""
    },
    selectedNote() {
      return this.notes.find(note => note.id == this.selectedId)
    }
  },
  mounted() {
    this.addNote()
  },
  data () {
    return {
      notes: [],
      selectedId: null
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

#nav-bar {
  position: fixed;
  width: 200px;
  height: 100vh;
  left: 0;
  right: 0;
  overflow-y: scroll;
}

.nav-action {
  padding: 25px 0 12px 0;
}

.nav-note {
  padding-top: 20px;
  padding-bottom: 20px;
  padding-left: 20px;
  border-bottom: 1px solid rgba(0,0,0,.1);
}

.nav-note:hover {
  background-color: lightgrey;
}

.nav-divider {
  padding: 0;
  border: none;
  margin-top: 12px;
  margin-bottom: 0px;
  border-bottom: 1px solid rgba(0,0,0,.1);
}

#note-content {
  position: relative;
  height: 100vh;
  margin-left: 200px;
  overflow-y: scroll;
}

.textarea-wrapper {
  width: 100%;
  height: 100%;
  border-right: 1px solid lightgrey;
  border-left: 1px solid lightgrey;
}

textarea {
  padding: 20px;
  height: 100%;
  width: 100%;
  border: none;
  resize: none !important;
  outline: none;
}

.preview-bg {
  background-color: #ddd;
}

.preview {
  padding: 20px;
  height: 100vh;
  position: relative;
  overflow-y: scroll;
}
</style>
