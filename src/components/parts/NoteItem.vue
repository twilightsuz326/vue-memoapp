<template>
    <div class="note-family">
        <div class="note" 
            @mouseover="onMouseOver" 
            @mouseleave="onMouseLeave"
            @click="onSelect(note)"
            v-bind:class="{ mouseover: note.mouseover && !note.editing, selected: note.selected }"
            >
            <template v-if="note.editing">
                <input v-model="noteName" class="transparent" @keypress.stop.enter="onEditEnd" />
            </template>
            <template v-else>
                <div class="note-icon">
                    <i class="fas fa-file-alt"></i>
                </div>
                <div class="note-name">{{ note.name }}</div>

                <div v-show="note.mouseover" class="buttons">
                    <div class="button-icon" v-if="layer < 3" @click="onclickChildNote(note)">
                        <i class="fas fa-sitemap"></i>
                    </div>
                    <div class="button-icon" @click="onClickAddNoteAfter(parentNote, note)">
                        <i class="fas fa-plus-circle"></i>
                    </div>
                    <div class="button-icon" @click="onClickEdit(note)">
                        <i class="fas fa-edit"></i>
                    </div>
                    <div class="button-icon" @click="onClickDelete(parentNote, note)">
                        <i class="fas fa-trash"></i>
                    </div>
                </div>
            </template>
        </div>
        <div class="child-note">
            <draggable @update="onDraggableUpdate" group="notes">
            <NoteItem
            v-for="childNote in note.children"
            v-bind:note="childNote"
            v-bind:layer="layer + 1"
            v-bind:parentNote="note"
            v-bind:key="childNote.id"
            @delete="onDeleteNote"
            @select="onSelect"
            @editStart="onEditNoteStart" 
            @editEnd="onEditNoteEnd" 
            @mouseOver="onNoteMouseOver" 
            @mouseLeave="onNoteMouseLeave"
            @addChild="onAddChildNote"
            @addNoteAfter="onClickAddNoteAfter"
            @edit="onEditNote"
            />
            </draggable>
        </div>
    </div>
</template>

<script>
import draggable from 'vuedraggable'

export default {
    name: 'NoteItem',
    props: [
        'note',
        'parentNote',
        'layer'
    ],
    computed: {
        noteName: {
            get: function () {
                return this.note.name;
            },
            set: function (value) {
                this.$emit('edit', this.note, value);
            }
        }
    },
    methods: {
        onMouseOver: function () {
            this.$emit('mouseOver', this.note);
        },
        onMouseLeave: function () {
            this.$emit('mouseLeave', this.note);
        },
        onSelect: function (note) {
            this.$emit('select', note);
        },
        onClickDelete: function (parentNote, note) {
            this.$emit('delete', parentNote, note);
        },
        onClickEdit: function (note) {
            this.$emit('editStart', note);
        },
        onEditEnd: function () {
            this.$emit('editEnd');
        },
        onclickChildNote: function (note) {
            this.$emit('addChild', note);
        },
        onClickAddNoteAfter: function (parentNote, note) {
            this.$emit('addNoteAfter', parentNote, note);
        },
        onDeleteNote: function (deleteNote) {
            this.$emit('delete', deleteNote);
        },
        onEditNoteStart: function (editNote) {
            this.$emit('editStart', editNote);
        },
        onEditNoteEnd: function () {
            this.$emit('editEnd');
        },
        onNoteMouseOver: function (hoveredNote) {
            this.$emit('mouseOver', hoveredNote);
        },
        onNoteMouseLeave: function (hoveredNote) {
            this.$emit('mouseLeave', hoveredNote);
        },
        onAddChildNote: function (note) {
            this.$emit('addChild', note);
        },
        onEditNote: function (editNote, value) {
            this.$emit('edit', editNote, value);
        },
        onDraggableUpdate: function (newChildrenOrder) {
            this.$emit('dragupdate', newChildrenOrder);
        }
    },
    components: {
        draggable,
    }
}
</script>

<style scoped lang="scss">
.note {
    margin: 10px 0;
    display: flex;
    align-items: center;
    padding: 5px;
    color: rgba(25, 23, 17, 0.6);

    &.mouseover {
        background-color: rgb(232, 231, 228);
        cursor: pointer;
    }

    &.selected {
        color: black;
        background-color: rgb(232, 231, 228);
        font-weight: 600;
    }

    .note-icon {
        margin-left: 10px;
    }

    .note-name {
        width: 100%;
        padding: 3px 10px;
    }

    .buttons {
        display: flex;
        flex-direction: row;

        .button-icon {
            padding: 3px;
            margin-left: 3px;
            border-radius: 5px;
        }
    }
}
.child-note {
    padding-left: 10px;
}
</style>