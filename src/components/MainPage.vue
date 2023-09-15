<template>
  <div class="main-page">
    <div class="left-menu">
      <!-- ノートリスト -->
      <NoteItem
        v-for="note in noteList"
        v-bind:key="note.id"
        v-bind:note="note"
        @mouseover="note.mouseover = $event"
        @delete="onDeleteNote"
      />
      <!-- ノート追加ボタン -->
        <button class="transparent" @click="onClickButtonAdd">
          <i class="fas fa-plus-square"></i>ノートを追加
        </button>
    </div>
    <div class="right-view">
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
      });
    },
    onDeleteNote: function (id) {
      const index = this.noteList.indexOf(id);
      this.noteList.splice(index, 1);
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