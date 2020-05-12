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
          <div class="note-date">
            {{getDate2(note)}}
          </div>
        </div>
      </div>
    </nav>

    <section id="note">

      <nav id="toolbar">
        <div class="row align-items-center">
          <div class="btn-bg btn-gutter" title="Create a note" @click="addNote">
            <svg class="btn-edit"></svg>
          </div>

          <div class="btn-bg btn-gutter" title="Delete" @click="removeNote">
            <svg class="btn-delete"></svg>
          </div>
      
          <h3 id="app-name">Notebook</h3>
        </div>
      </nav>

      <template v-if="selectedNote">
        <section id="note-content">
          <div class="row no-gutters">
            <div class="col-6">
              <!-- Text input pane -->
              <section class="textarea-wrapper">
                <div class="datetime text-center">
                  {{this.datetime}}
                </div>
                <div class="note-title">
                  <input placeholder="Title" v-model="selectedNote.title">
                </div>
                <textarea placeholder="Write here" v-model="selectedNote.content"></textarea>
              </section>
            </div>
            <div class="col-6">
              <!-- Preview pane -->
              <section class="preview" v-html="notePreview"></section>
            </div>
          </div>
          
        </section>
      </template>

    </section>
   
  </div>
</template>

<script>
import marked from "marked"
const monthNames = ["January", "February", "March", "April", "May", "June",
  "July", "August", "September", "October", "November", "December"
]

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
      this.notes.unshift(note)
      this.selectedId = note.id
    },
    removeNote() {
      if (this.selectedNote) {
        const index = this.notes.indexOf(this.selectedNote)
        if (index !== -1) {
          this.notes.splice(index, 1)
          if (index < this.notes.length) {
            this.selectNote(this.notes[index])
          } else if (this.notes.length > 0) {
            this.selectNote(this.notes[index-1])
          }
        }
      }
    },
    selectNote(note) {
      this.selectedId = note.id
    },
    getDate2(note) {
      const dt = new Date()
      dt.setTime(note.created)
      return dt.getMonth() + "/" + dt.getDate() + "/" + dt.getFullYear()
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
    },
    datetime() {
      const dt = new Date()
      dt.setTime(this.selectedNote.created)
      const date = monthNames[dt.getMonth()] + " " + dt.getDate() + ", " + dt.getFullYear()
      
      let time = ""
      const hours = dt.getHours()
      let minutes = dt.getMinutes()
      if (minutes < 10) {
        minutes = "0" + minutes
      }

      if (dt.getHours() > 12) {
        time = hours-12 + ":" + minutes + " PM"
      } else {
        time = hours + ":" + minutes + " AM"
      }
      return date + " at " + time
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
  height: 100%;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

* {
  background-color: #fafafa;
}

#nav-bar {
  position: fixed;
  height: 100vh;
  width: 225px;
  overflow-y: scroll;
  border-right: 1px solid lightgrey;
}

#note {
  margin-left: 225px;
  display: flex;
  flex-flow: column;
  height: 100%;
}

#toolbar {
  padding: 8px 10px 8px 10px;
  border-bottom: 1px solid lightgrey;
}

#note-content {
  flex-grow: 1;
}

.nav-action {
  padding: 25px 0 12px 0;
}

.nav-note {
  padding: 12px 20px 12px 20px;
  border-bottom: 1px solid rgba(0,0,0,.1);
  cursor: pointer;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  user-select: none;
}

.note-date {
  font-size: 13px;
  background-color: inherit;
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

#app-name {
  margin-right: 10px;
  margin-left: auto;
  margin-bottom: 0;
  user-select: none;
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
  background-color: inherit;
  height: 20px;
  width: 20px;
  background-image: url("./assets/edit.svg");
}

.btn-delete {
  background-color: inherit;
  height: 20px;
  width: 20px;
  background-image: url("./assets/trash.svg");
}

#note-content .row {
  height: 100%;
}

.datetime {
  font-size: 13px;
  color: gray;
  padding-top: 5px;
  padding-bottom: 5px;
}

#note-content .note-title {
  padding: 0 15px 0 15px;
}

.note-title input {
  width: 100%;
  border: 0;
  border-bottom: 1px solid lightgray;
  outline: none;
  height: 40px;
}

.textarea-wrapper {
  height: 100%;
  width: 100%;
  border-right: 1px solid lightgrey;
  display: flex;
  flex-flow: column;
}

textarea {
  padding: 10px 15px 15px 15px;
  flex-grow: 1;
  width: 100%;
  border: none;
  resize: none !important;
  outline: none;
}

.preview {
  padding: 15px 15px 15px 15px;
  position: absolute;
  height: 100%;
  overflow-y: scroll;
}
</style>
