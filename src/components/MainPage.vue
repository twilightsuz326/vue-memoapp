<template>
  <div class="main-page">
    <div class="left-menu" @click.self="onEditNoteEnd()">
      <!-- ノートリスト -->
      <NoteItem 
      v-for="note in noteList" 
      v-bind:note="note" 
      v-bind:key="note.id" 
      @delete="onDeleteNote"
      @editStart="onEditNoteStart" 
      @editEnd="onEditNoteEnd" 
      @mouseOver="onNoteMouseOver" 
      @mouseLeave="onNoteMouseLeave"
      @addChild="onAddChildNote"
      />

      <!-- ノート追加ボタン -->
      <button class="transparent" @click="onClickButtonAdd">
        <i class="fas fa-plus-square"></i>ノートを追加
      </button>
    </div>
    <div class="right-view" @click.self="onEditNoteEnd()">
      右ビュー
    </div>
  </div>
</template>

<script>
import NoteItem from '@/components/parts/NoteItem.vue'

export default {
  data() {
    return {
      noteList: [],
    }
  },
  methods: {
    onClickButtonAdd: function () {
      this.noteList.push({
        id: new Date().getTime().toString(16),
        name: `新規ノート`,
        mouseover: false,
        editing: false,
        children: [],
      })
    },
    onDeleteNote: function (deleteNote) {
      const removeNote = (notes) => {
        const index = notes.findIndex(note => note.id === deleteNote.id);
        if (index > -1) {
          notes.splice(index, 1);
          return true;
        }

        for (let note of notes) {
          if (note.children && removeNote(note.children)) return true;
        }
        return false;
      }

      removeNote(this.noteList);
    },
    onEditNoteStart: function (editNote) {
      const setEditingStatusRecursively = (notes, targetNoteId, status) => {
        for (let note of notes) {
          if (note.id === targetNoteId) {
            note.editing = status;
            return true;
          } else if (note.children && note.children.length > 0) {
            const isUpdated = setEditingStatusRecursively(note.children, targetNoteId, status);
            if (isUpdated) return true;
          }
        }
        return false;
      }

      setEditingStatusRecursively(this.noteList, editNote.id, true);
    },
    onEditNoteEnd: function () {
      for (let note of this.noteList) {
        note.editing = false;
      }
      // 子ノートの編集状態をリセット
      const resetEditingStatusRecursively = (notes) => {
        for (let note of notes) {
          note.editing = false;
          if (note.children && note.children.length > 0) {
            resetEditingStatusRecursively(note.children);
          }
        }
      }
      resetEditingStatusRecursively(this.noteList);
    },
    onNoteMouseOver: function (hoveredNote) {
      this.updateMouseOverStatus(this.noteList, hoveredNote.id, true);
    },
    onNoteMouseLeave: function (hoveredNote) {
      this.updateMouseOverStatus(this.noteList, hoveredNote.id, false);
    },
    onAddChildNote: function (note) {
      note.children.push({
        id: new Date().getTime().toString(16),
        name: note.name + 'の子',
        mouseover: false,
        editing: false,
        children: [],
      });
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
  },
  components: {
    NoteItem,
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