<template>
    <td 
        v-for="(elem,index) in list" 
        :key="`${elem}_${index}`"
    >
        <div v-if="option === 'elem'" class="elem">
            <input
                :id = "index"
                class="checkBox"
                type="checkbox"
                @change="(e) => $emit('setActive', e, elem)"
            />
            <div>
                <img :src="getSrc(types[index])"/>
            </div>
            <span
                :id = "index"
                :class = "{'active' : active.includes(elem)}"
                @click="$emit('openDir', elem)"
            >
                {{elem.name}}
            </span>
        </div>  
        <div 
            v-else-if="option === 'size'" 
            class="elem"
        >
            <div 
                v-if="elem.size !== '0.1kB'"
                class="elem" 
            >
                {{elem.size}}
            </div>
            <div v-else class="elem"></div>
        </div>
        <div 
            v-else-if="option === 'date'" 
            class="elem"
        >
            <div class="elem">{{elem.mtime}}</div>
        </div>
        <div 
            v-else-if="option === 'type'" 
            class="elem"
        >
            <div 
                v-if="elem === 'Папка с файлами'" 
                class="elem"
            >
                {{elem}}
            </div>
            <div v-else class="elem">Файл: {{elem}}</div>
        </div>
        <div v-else>
            <p class="elem">{{index+1}}</p>
        </div>
    </td>
</template>

<script>
    export default {
        emits: ['openDir', 'setActive'],
        props: {
            list: {
                type: Array,
                required: true
            },
            types: {
                type: Array
            },
            active: {
                type: Array
            },
            option: {
                type: String,
                required: true
            }
        },
        methods: {
            getSrc(type) {
                switch(type) {
                    case 'Папка с файлами': {
                        return require('@/assets/icons/folder.jpg')
                    }
                    case 'sys' : {
                        return require('@/assets/icons/sys.png')
                    }
                    case 'txt' : {
                        return require('@/assets/icons/txt.png')
                    }
                    case 'dll' : {
                        return require('@/assets/icons/dll.png')
                    }
                    case 'db' : {
                        return require('@/assets/icons/db.png')
                    }
                    case 'config' : {
                        return require('@/assets/icons/config.png')
                    }
                    case 'ini' : {
                        return require('@/assets/icons/ini.png')
                    }
                    case 'mp3' : {
                        return require('@/assets/icons/mp3.png')
                    }
                    case 'mp4' || 'ASF' || 'WMA' || 'WMV' || 'WM' : {
                        return require('@/assets/icons/mp4.png')
                    }
                    default: {
                        return require('@/assets/icons/file.png')
                    }
                }
            }
        }
    }
</script>

<style scoped>
.elem {
    display: flex;
    flex-direction: row;
    justify-content:flex-start;
    width: 100%;
    height: fit-content;
    align-items: center;
    height: 40px;
}
.elem div {
    margin-left: 5px;
    display: flex;
    align-items: center;
}
.elem input {
    width: 20px;
    height: 20px;
}
.elem img {
    width: 20px;
    height: 20px;
}
.elem span {
    width: auto;
    word-wrap: break-word;
    padding: 2px;
}
.active {
    background-color: rgba(4, 28, 245, 0.733);
    color: #fff;
    border-radius: 2px;
}
</style>