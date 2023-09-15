<template>
    <div class="note" @mouseover="onMouseOver" @mouseleave="onMouseOut" v-bind:class="{ 'mouseover': note.mouseover }">
        <div class="note-icon">
            <i class="fas fa-file-alt"></i>
        </div>
        <div class="note-name">{{ note.name }}</div>

        <div v-show="note.mouseover" class="buttons">
            <div class="button-icon">
                <i class="fas fa-sitemap"></i>
            </div>
            <div class="button-icon">
                <i class="fas fa-plus-circle"></i>
            </div>
            <div class="button-icon">
                <i class="fas fa-edit"></i>
            </div>
            <div class="button-icon" @click="onDeleteNote(note)">
                <i class="fas fa-trash"></i>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'NoteItem',
    props: [
        'note',
    ],
    methods: {
        onMouseOver: function () {
            this.$emit('mouseover', true);
        },
        onMouseOut: function () {
            this.$emit('mouseover', false);
        },
        onDeleteNote: function () {
            this.$emit('delete', this.note.id);
        },
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
        background-color: #d8d6d6;
        color: rgb(48, 48, 48);
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
        align-items: center;
        margin-left: auto;
        margin-right: 10px;
        .button-icon {
            margin-left: 10px;
        }
    }
}
</style>