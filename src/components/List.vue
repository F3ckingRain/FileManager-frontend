<template>
<div class="table">
    <table>
        <tr class="column" id="list">
            <td></td>
            <Column :list = "sortedList" :option="`count`"/>
        </tr>
        <tr class="column" id= "info">
            <td class="first">
                <div class="header">
                    <select v-model="currentDisk">
                        <option>C:</option>
                        <option>D:</option>
                    </select>
                    <Editor 
                        :cuted="cuted" 
                        :copied="copied" 
                        :pathLength = "currentPathLength"
                        @copy="copyFiles" 
                        @cbc="copyBeforeCut"
                        @paste = "pasteFiles" 
                        @delete = "deleteFile"
                        @cut = "cutFiles"
                        @back = "goBack"
                    />
                </div>
            </td>
            <Column 
                :list = "sortedList" 
                :option = "`elem`" 
                :types = "types" 
                :active="active" 
                @openDir="openDir"
                @setActive = "setActive"
            />
        </tr>
        <tr class="column" id = "options">
            <td class="sec">
                <div class="content">Тип: </div>
            </td>
            <Column :list= "types" :option="`type`"/>
        </tr>
        <tr class="column" id="date">
            <td class="third">
                <div class = "content">Дата изменения: </div>
            </td>
            <Column :list="sortedList" :option="`date`"/>
        </tr>
        <tr class="column" id = "size">
            <td class="fourth">
                <div class="content">Размер: </div>
            </td>
            <Column :list = "sortedList" :option = '`size`'/>
        </tr>
    </table>
    <div class="elements">
        <div>Элементов: {{currentList.length}}</div>
        <div v-if="activeLength > 0">
            | Выбрано элементов: {{activeLength}}
        </div>
    </div>
</div>
</template>

<script>
import axios from 'axios'
import Column from './Column.vue'
import Editor from './Editor.vue'
    export default {
        components: {
            Column,
            Editor
        },
        props: {
            disk: {
                type: String,
                required: true
            },
            copiedList: {
                type: Array
            },
            cutedList: {
                type: Array
            },
            copied: {
                type: Boolean
            },
            cuted: {
                type: Boolean
            },
            listTrack: {
                type: Boolean
            }
        },
        data() {
            return {
                currentDisk: `${this.disk}`,
                currentPath : [this.disk],
                currentList: [],
                active: [],
            }
        },
        methods: {
            async refreshDirectory() {
                try {
                    const response = await axios.post(
                        'http://localhost:3123/ls', 
                        {path: this.currentPath}
                    )
                    this.currentList = response.data
                    this.active = []
                    this.disableAllCheckers()
                } catch(e) {
                    alert("Не удалось загрузить данные")
                }
            },
            async deleteFile() {
                const files = this.active.map((file) => ({
                    path: [...this.currentPath, file.name],
                    isDir: file.type === 'dir'
                }));
                try {
                    await axios.post('http://localhost:3123/dl', {files})
                    this.refreshDirectory()
                } catch(e) {
                    alert('Не удалось удалить файл(ы)')
                }
            },
            async cutFiles() {
                try {   
                    await axios.post(
                        'http://localhost:3123/mv', 
                        {files: this.copiedList, path: this.currentPath}
                    )
                    this.$emit('sendBool', {type: 'cut', bool: false})
                    this.$emit('sendTrack', true)
                } catch(e) {
                    alert('Не удалось переместить файл(ы)')
                }
            },
            copyBeforeCut() {
                if (this.activeLength > 0) {
                    this.$emit('sendBool', {type: 'cut', bool: true})
                    this.$emit('sendList', this.arrToSend) 
                }
            },
            copyFiles() {
                if(this.activeLength > 0)  {
                    this.$emit('sendBool', {type: "copy", bool: true})
                    this.$emit('sendList', this.arrToSend)
                }
            },
            async pasteFiles() {
                try {
                    await axios.post(
                        'http://localhost:3123/cp', 
                        {files: this.copiedList, path: this.currentPath}
                    )
                    this.$emit('sendBool', {type: 'copy', bool: false})
                    this.$emit('sendTrack', true)
                    this.refreshDirectory()
                } catch(e) {
                    alert('Не удалось вставить файл(ы)')
                }
            },
            openDir(elem) {
                if (elem.type === 'dir') {
                    try {
                        this.currentPath.push(elem.name)
                    } catch(e) {
                        alert('Не удалось открыть папку')
                    }
                }
            },
            goBack() {
                if (this.currentPathLength > 0) {
                    this.currentPath.pop()
                }
            },
            setActive(e, elem) {
                if (e.target.checked) {
                    this.active.push(elem)
                } else {
                    this.active = this.active.filter(el => el !== elem);
                }
            },
            disableAllCheckers() {
                Array.from(document.getElementsByClassName('checkBox'))
                .forEach(el => el.checked = false)

                Array.from(document.getElementsByClassName('active'))
                .forEach(el => el.setAttribute('class', ''))
            },
            
        },
        computed: {
            currentPathLength() {
                return this.currentPath.length
            },
            sortedList() {
                return this.currentList.sort((a,b) => a.type
                .localeCompare(b.type))
            },
            activeLength() {
                return this.active.length
            },
            types() {
                const typesArr = []
                for(let i = 0; i < this.sortedList.length; i++) {
                    if (this.sortedList[i].type === 'dir') {
                        typesArr.push('Папка с файлами')
                    } else {
                        typesArr.push(this.sortedList[i].ext)
                    }
                }
                return typesArr
            },
            arrToSend() {
                return this.active.map((file) => ({
                    name: file.name,
                    path: [...this.currentPath, file.name],
                    isDir: file.type === 'dir'
                }))
            },
            copiedListLength() {
                return this.copiedList.length
            },
            cutedListLength() {
                return this.cutedList.length
            }
        },
        watch: {
            currentPath() {
                this.refreshDirectory()
            },
            currentDisk() {
                this.currentPath = [this.currentDisk]
            },
            currentPathLength() {
                this.refreshDirectory()
            },
            listTrack() {
                this.refreshDirectory()
                this.$emit('sendTrack', false)
            }
        },
        mounted() {
            this.refreshDirectory()
        }
    }
</script>

<style  lang="scss" scoped>
$main-color : rgba(255, 174, 0, 0.952);
.table { 
    display: flex;
    margin: 5px;
    flex-direction: column;
    border: 10px ridge;
    border-color: $main-color;
    border-radius: 5px;
    width:49%;
    justify-content: space-between;
    height: fit-content;

    table {
        display: flex;
        flex-direction: row;
        justify-content: space-between;

        td {
            height: 50px;
            display: flex;
            align-items: center;
            margin: 5px;
        }
    }
}

.elements {
    width: 300px;
    height: 20px;
    display: flex;
    align-items: flex-end;
    justify-content: flex-start;
    flex-direction: row;
    background-color: $main-color ;
    border-radius: 0px 15px 0px 0px;
    font-size: 16px;
}
.elements {
    div {
        margin-left: 5px;
    }   
}
.column {
    display: flex;
    flex-direction: column;
}
.header {
    width: 100%;
    display: flex;
    flex-direction: row;
    justify-content: space-between;

    select {
        width: 45px;
        height: 24px;
    }
}
.content {
    border: 1px solid;
    padding: 3px;
    border-radius: 2px;
    width: fit-content;
}
#list {
    display: flex;
    align-items: center;
    width: 5%;
}
#info {
    width: 50%
}
#options {
    width: 20%;
}
#date {
    width: 15%;
}
#size {
    width: 10%
}
</style>
