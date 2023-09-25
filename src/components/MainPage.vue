<template>
  <div class="main-page">
    <div class="left-menu" @click.self="onEditNoteEnd()">
      <draggable v-bind:list="noteList" group="notes">
      <!-- ノートリスト -->
      <NoteItem 
      v-for="note in noteList" 
      v-bind:note="note" 
      v-bind:key="note.id" 
      v-bind:layer="1"
      @delete="onDeleteNote"
      @select="onSelectNote"
      @editStart="onEditNoteStart" 
      @editEnd="onEditNoteEnd" 
      @mouseOver="onNoteMouseOver" 
      @mouseLeave="onNoteMouseLeave"
      @addChild="onAddChildNote"
      @addNoteAfter="onAddNoteAfter"
      @edit="onEditNote"
      @dragupdate="onDraggableUpdate"
      />
      </draggable>

      <!-- ノート追加ボタン -->
      <button class="transparent" @click="onClickButtonAdd">
        <i class="fas fa-plus-square"></i>ノートを追加
      </button>
    </div>
    <div class="right-view" @click.self="onEditNoteEnd()">
      <template v-if="selectedNote">
        <div class="path">
          <small>{{ selectedPath }}</small>
        </div>
        <div class="note-content">
          <h3 class="note-title">{{ selectedNote.name }}</h3>
        </div>
      </template>
      <template v-else>
        <div class="no-selected-note">
          ノートを選択してください
        </div>
      </template>
    </div>
  </div>
</template>

<script>
import NoteItem from '@/components/parts/NoteItem.vue'
import draggable from 'vuedraggable'

export default {
  data() {
    return {
      noteList: [],
      selectedNote: null,
    }
  },
  methods: {
    onAddNoteCommon: function (targetList, layer, index) {
      layer = layer || 1;
      const note = {
        id: new Date().getTime().toString(16),
        name: `新規ノート-${layer}-${targetList.length}`,
        mouseover: false,
        editing: false,
        selected: false,
        children: [],
        layer: layer,
      };
      if (index == null) {
        targetList.push(note);
      } else {
        targetList.splice(index + 1, 0, note);
      }
    },
    onClickButtonAdd: function () {
      this.onAddNoteCommon(this.noteList);
    },
    onDeleteNote: function (parentNote, deleteNote) {
      const targetList = parentNote == null ? this.noteList : parentNote.children;
      const deleteIndex = targetList.indexOf(deleteNote);
      targetList.splice(deleteIndex, 1);
    },
    onSelectNote: function (targetNote) {
      // 再帰的にノート選択状態を更新
      const updateSelectStatus = function(targetNote, noteList) {
        for (let note of noteList) {
          note.selected = (note.id === targetNote.id);
          updateSelectStatus(targetNote, note.children);
        }
      };
      updateSelectStatus(targetNote, this.noteList);
      this.selectedNote = targetNote;
    },
    onEditNoteStart: function (editNote, parentNote) {
      const targetList = parentNote == null ? this.noteList : parentNote.children;
      for (let note of targetList) {
        note.editing = (note.id === editNote.id);
        this.onEditNoteStart(editNote, note);
      }
    },
    onEditNoteEnd: function (parentNote) {
      const targetList = parentNote == null ? this.noteList : parentNote.children;
      for (let note of targetList) {
        note.editing = false;
        this.onEditNoteEnd(note);
      }
    },
    onNoteMouseOver: function (hoveredNote) {
      this.updateMouseOverStatus(this.noteList, hoveredNote.id, true);
    },
    onNoteMouseLeave: function (hoveredNote) {
      this.updateMouseOverStatus(this.noteList, hoveredNote.id, false);
    },
    onAddChildNote: function (note) {
      this.onAddNoteCommon(note.children, note.layer + 1);
    },
    onAddNoteAfter: function (parentNote, note) {
      const targetList = parentNote == null ? this.noteList : parentNote.children;
      const layer = parentNote == null ? 1 : note.layer;
      const index = targetList.indexOf(note);
      this.onAddNoteCommon(targetList, layer, index);
    },
    updateMouseOverStatus: function (notes, noteId, status) {
      for (let note of notes) {
        if (note.id === noteId) {
          note.mouseover = status;
        } else if (note.children && note.children.length > 0) {
          this.updateMouseOverStatus(note.children, noteId, status);
        }
      }
    },
    onEditNote: function (editNote, value) {
      editNote.name = value;
    },
    onDraggableUpdate: function (event) {
      this.noteList = event;
    },
  },
  computed: {
    selectedPath: function () {
      const searchSelectedPath = function(noteList, path) {
        for (let note of noteList) {
          const currentPath = path == null ? note.name : `${path} > ${note.name}`;
          if (note.selected) return currentPath;
          const selectedPath = searchSelectedPath(note.children, currentPath);
          if (selectedPath.length > 0) return selectedPath;
        }
        return '';
      }
      return searchSelectedPath(this.noteList);
    },
  },
  components: {
    NoteItem,
    draggable,
  },
}
</script>

<style scoped lang="scss">
.main-page {
  display: flex;
  height: calc(100vh - 60px);

  .left-menu {
    width: 350px;
    background-color: #f7f6f3;
  }

  .right-view {
    flex-grow: 1;
    padding: 10px;
  }
}
</style>