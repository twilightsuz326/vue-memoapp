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
      @editWidgettxt="onEditWidgettxt"
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
          <WidgetItem
              v-for="widget in selectedNote.widgetList"
              v-bind:widget="widget"
              v-bind:layer="1"
              v-bind:key="widget.id"
              @delete="onDeleteWidget"
              @addChild="onAddChildWidget"
              @addWidgetAfter="onAddWidgetAfter"
              @parentMouseOver="onWidgetMouseOver"
              @parentMouseLeave="onWidgetMouseLeave"
              @childMouseOver="onWidgetMouseOver"
              @childMouseLeave="onWidgetMouseLeave"
            />
          <button class="transparent" @click="onClickButtonAddWidget">
            <i class="fas fa-plus-square"></i>ウィジェットを追加
          </button>
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
import WidgetItem from '@/components/parts/WidgetItem.vue'
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
        widgetList: [],
      };
      this.onAddWidgetCommon(note.widgetList);
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
    onEditWidgettxt: function (editWidget, value) {
      editWidget.text = value;
    },
    onWidgetMouseOver: function (event) {
      console.log(event)
      for (let widget of this.selectedNote.widgetList) {
        if (widget.id === event.id) {
          widget.mouseover = true;
        } else if (widget.children && widget.children.length > 0) {
          this.updateMouseOverStatus(widget.children, event.id, true);
        }
      }
    },
    onWidgetMouseLeave: function (widget) {
      widget.mouseover = false;
    },
    onDraggableUpdate: function (event) {
      this.noteList = event;
    },
    onAddWidgetCommon: function (targetList, layer, index) {
      layer = layer || 1;
      const widget = {
        id: new Date().getTime().toString(16),
        type: 'heading',
        text: '',
        mouseover: false,
        children: [],
        layer: layer,
      };
      if (index == null) {
        targetList.push(widget);
      } else {
        targetList.splice(index + 1, 0, widget);
      }
    },
    onClickButtonAddWidget: function () {
      this.onAddWidgetCommon(this.selectedNote.widgetList);
    },
    onAddChildWidget: function (widget) {
      this.onAddWidgetCommon(widget.children, widget.layer + 1);
    },
    onAddWidgetAfter: function (parentWidget, note) {
      const targetList = parentWidget == null ? this.selectedNote.widgetList : parentWidget.children;
      const layer = parentWidget == null ? null : parentWidget.layer + 1;
      const index = targetList.indexOf(note);
      this.onAddWidgetCommon(targetList, layer, index);
    },
    onDeleteWidget: function (parentWidget, widget) {
      const targetList = parentWidget == null ? this.selectedNote.widgetList : parentWidget.children;
      const index = targetList.indexOf(widget);
      targetList.splice(index, 1);
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
    WidgetItem,
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