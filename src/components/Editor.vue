<template>
    <div class="editor">
        <img v-if="!copied" 
            class="icon" 
            src='@/assets/icons/copy.png' 
            @click="$emit('copy')"
        />
        <img v-else 
            class="icon" 
            src="@/assets/icons/paste.png" 
            @click="$emit('paste')" 
        />
        <img 
            src="@/assets/icons/delete.png" 
            class="icon" 
            @click="$emit('delete')" 
        />
        <img v-if="!cuted" 
            src="@/assets/icons/cut.png" 
            class="icon" 
            @click="$emit('cbc')" 
        />
        <img v-else 
            class="icon" 
            src="@/assets/icons/paste.png" 
            @click="$emit('cut')" 
        />
        <img 
            v-show="pathLength > 1"
            src="@/assets/icons/GoBack.png" 
            @click="$emit('back')" 
            class="icon" 
        />
    </div>
</template>

<script>
export default {
    emits: ['copy', 'cut', 'paste', 'delete', 'cbc'],
    props: {
        cuted: {
            type: Boolean,
            required: true
        },
        copied: {
            type: Boolean,
            required: true
        },
        pathLength: {
            type: Number,
            required: true
        }
    }
}
</script>

<style  scoped>
.icon {
    border: 1px solid;
    border-radius: 3px;
    padding: 2px;
    width: 21px;
    height: 18px;
}
.editor {
    margin-left: 5px;
    margin-right: 5px;
    display: flex;
    flex-direction: row;
    width: 100%;
    justify-content: space-evenly;
}
</style>