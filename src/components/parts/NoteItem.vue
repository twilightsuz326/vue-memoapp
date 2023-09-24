<template>
    <div class="note-family">
        <div class="note" @mouseover="onMouseOver" @mouseleave="onMouseLeave"
            v-bind:class="{ mouseover: note.mouseover && !note.editing }">
            <template v-if="note.editing">
                <input v-model="noteName" class="transparent" @keypress.stop.enter="onEditEnd" />
            </template>
            <template v-else>
                <div class="note-icon">
                    <i class="fas fa-file-alt"></i>
                </div>
                <div class="note-name">{{ note.name }}</div>

                <div v-show="note.mouseover" class="buttons">
                    <div class="button-icon" @click="onclickChildNote(note)">
                        <i class="fas fa-sitemap"></i>
                    </div>
                    <div class="button-icon">
                        <i class="fas fa-plus-circle"></i>
                    </div>
                    <div class="button-icon" @click.stop="onClickEdit(note)">
                        <i class="fas fa-edit"></i>
                    </div>
                    <div class="button-icon" @click.stop="onClickDelete(parentNote, note)">
                        <i class="fas fa-trash"></i>
                    </div>
                </div>
            </template>
        </div>
        <div class="child-note">
            <NoteItem
            v-for="childNote in note.children"
            v-bind:note="childNote"
            v-bind:parentNote="note"
            v-bind:key="childNote.id"
          @delete="onDeleteNote"
          @editStart="onEditNoteStart" 
          @editEnd="onEditNoteEnd" 
          @mouseOver="onNoteMouseOver" 
          @mouseLeave="onNoteMouseLeave"
          @addChild="onAddChildNote"
            />
        </div>
    </div>
</template>

<script>
export default {
    name: 'NoteItem',
    props: [
        'note',
        'parentNote',
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
    },
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