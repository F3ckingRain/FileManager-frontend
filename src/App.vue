<template>
  <div class="container">
    <List 
      :disk="firstPath" 
      @sendList = "getList" 
      @sendBool = "getBool"
      @sendTrack = "getTrack"
      :copiedList="copiedList" 
      :cutedList = "cutedList"
      :copied="copied"
      :cuted = "cuted"
      :listTrack = "listTrack"
    />
    <List 
      :disk="secPath" 
      @sendList = "getList" 
      @sendBool = "getBool"
      @sendTrack = "getTrack"
      :copiedList="copiedList" 
      :cutedList = "cutedList"
      :copied="copied"
      :cuted = "cuted"
      :listTrack = "listTrack"
    />
  </div>
</template>

<script>
import List from '@/components/List'
export default {
  name: 'App',
  components: {
    List
  },
  data() {
    return {
      firstPath: 'C:',
      secPath: 'D:',
      copiedList: [],
      cutedList: [],
      copied: false,
      cuted: false,
      listTrack: false
    }
  },
  methods: {
    getList(arr) {
      this.copiedList = arr
    },
    getCutedList(arr) {
      this.cutedList = arr
    },
    getBool(obj) {
      obj.type === 'cut' 
      ? this.cuted = obj.bool 
      : this.copied = obj.bool
    },
    getTrack(bool) {
      this.listTrack = bool
    }
  }
}
</script>
<style>
* {
  margin: 0;
  padding: 0;
}
#app {
  width: 100%;
  height: 100%;
  font-size: 14px;
}
.container {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
span {
  margin: 10px;
}
</style>
