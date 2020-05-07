<template>
  <div id="app"> 
  <!-- Main app -->

    <nav id="nav-bar">
      <div>
        <div class="nav-note" 
        v-for="note of notes" :key="note.id"
        @click="selectNote(note)"
        :class="{selected: note == selectedNote}">
          {{note.title}}
        </div>
      </div>
    </nav>

    <nav id="toolbar">
      <div class="row align-items-center">
        <div class="btn-bg btn-gutter" @click="addNote">
          <svg class="btn-edit"></svg>
        </div>

        <div class="btn-bg btn-gutter" @click="removeNote">
          <svg class="btn-delete"></svg>
        </div>
      
        <h3 id="app-name">Notebook</h3>
      </div>
    </nav>
  
    <template v-if="selectedNote">
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
    </template>

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
    removeNote() {
      if (this.selectedNote) {
        const index = this.notes.indexOf(this.selectedNote)
        if (index !== -1) {
          this.notes.splice(index, 1)
        }
      }
    },
    selectNote(note) {
      this.selectedId = note.id
    },
    saveNotes() {
      localStorage.setItem("notes", JSON.stringify(this.notes))
    }
  },
  watch: {
    notes: {
      handler: "saveNotes",
      deep: true
    },
    selectedId(val) {
      localStorage.setItem("selected-id", val)
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
  data () {
    return {
      notes: JSON.parse(localStorage.getItem("notes")) || [],
      selectedId: localStorage.getItem("selected-id") || null
    }
  }
}
</script>

<style>
#app {
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
  top: 0;
  overflow-y: scroll;
  border-right: 1px solid lightgrey;
}

.nav-action {
  padding: 25px 0 12px 0;
}

.nav-note {
  padding-top: 20px;
  padding-bottom: 20px;
  padding-left: 20px;
  border-bottom: 1px solid rgba(0,0,0,.1);
  cursor: pointer;
}

.selected {
  background-color: lightgrey;
}

.nav-divider {
  padding: 0;
  border: none;
  margin-top: 12px;
  margin-bottom: 0px;
  border-bottom: 1px solid rgba(0,0,0,.1);
}

#toolbar {
  margin-left: 200px;
  padding: 8px 10px 8px 10px;
  border-bottom: 1px solid lightgrey;
}

#app-name {
  margin-right: 10px;
  margin-left: auto;
  margin-bottom: 0;
}

#toolbar .row {
  margin: 0;
}

.btn-bg {
  background-color: lightgray;
  border-radius: 4px;
  padding: 4px;
  cursor: pointer;
  width: 40px;
  text-align: center;
}

.btn-gutter {
  margin-right: 8px;
}

.btn-edit {
  height: 20px;
  width: 20px;
  background-image: url("./assets/edit.svg");
}

.btn-delete {
  height: 20px;
  width: 20px;
  background-image: url("./assets/trash.svg");
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
