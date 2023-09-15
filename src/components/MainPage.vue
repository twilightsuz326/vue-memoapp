<template>
  <div class="main-page">
    <div class="left-menu" @click.self="onEditNoteEnd()">
      <!-- ノートリスト -->
      <NoteItem
        v-for="note in noteList"
        v-bind:key="note.id"
        v-bind:note="note"
        @mouseover="note.mouseover = $event"
        @delete="onDeleteNote"
        @edit="onEditNote"
        @editstart="onEditNoteStart"
        @editend="onEditNoteEnd"
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
        id: new Date().getTime().toString(),
        name: '新しいノート',
        mouseover: false,
        editing: false,
      });
    },
    onDeleteNote: function (id) {
      this.noteList = this.noteList.filter(note => note.id !== id);
    },
    onEditNote: function (editNote, name) {
      editNote.name = name;
    },
    onEditNoteStart: function (editNote) {
      for (let note of this.noteList) {
        note.editing = false;
      }
      editNote.editing = true;
    },
    onEditNoteEnd: function () {
      for (let note of this.noteList) {
        note.editing = false;
      }
    },
  },
  components: {
    NoteItem,
  }
}
</script>

<style scoped lang="scss">
.main-page {
    display: flex;
    height: calc(100vh - 56px);
    .left-menu {
        width: 300px;
        background-color: #f5f5f5;
    }
    .right-view {
        flex-grow: 1;
        padding: 10px;
    }
}
</style>