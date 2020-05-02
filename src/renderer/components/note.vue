<template>
  <div id="myNote">
    <h2>{{message}}</h2>
    <h4>created in {{creatTime}}</h4>
    <ul class="notes">
      <li v-for="(item, index) in noteList" :key="index">
        <div :style="{background: item.background,color: item.color}">
            <span class="time">{{item.timeStamp | timeFormat}}</span>
            <div :style="{borderColor: item.color}" class="title">
              <input maxlength="15" v-model="item.title"  />
            </div>
            <div class="content">
              <textarea v-model="item.content"></textarea>		
            </div>
        </div>
        <span :tit="item.title" 
          :con="item.content" 
          :color="item.color" 
          :bgcolor="item.background" 
          @click="saveNote(item,$event)" class="saveNote">save</span>
        <span @click="deleteNote(item)" class="deleteNote">delete</span>
      </li>
    </ul>
    <span @click="showNote" class="addNew" :class="{addRotate: isRotate}">+</span>
    <div class="newNote" :style="{background: noteBgColor,color: noteColor}"  :class="{showNote: isShow}">
      <div @click="changeColor" class="customized color">
        <label class="description">文字</label>
					<span color="#f60" class="selected"></span>
					<span color="#000"></span>
					<span color="#eee4c9"></span>
					<span color="#ffa"></span>
      </div>
      <div @click="changeBgColor" class="customized bgcolor">
        <label class="description">背景色</label>
					<span color="#cdfbcd" class="selected"></span>
					<span color="#fcc"></span>
					<span color="#eee4c9"></span>
					<span color="#ffa"></span>
      </div>
      <div class="content">
        <input type="text" v-model="noteTitle" placeholder="这里是便签标题">
        <textarea placeholder="这里是便签内容" v-model="noteContent"></textarea>
        <span class="create" @click="createNote">create</span>
      </div>
    </div>
  </div>
</template>

<script>
import './note.css'
export default {
  data () {
    return {
      message: '在线便签',
      creatTime: '2017.04.07',
      noteList: [],
      isShow: false,
      isRotate: false,
      // 便签内容
      noteTitle: '',
      noteContent: '',
      noteColor: '',
      noteBgColor: ''
    }
  },
  // 初始化
  mounted () {
    this.showList()
  },
  filters: {
    // 格式化时间
    timeFormat (value) {
      let d = new Date(value)
      let yyyy = d.getFullYear()
      let MM = d.getMonth() + 1
      let dd = d.getDate()
      let hh = d.getHours()
      let mm = d.getMinutes()
      let ss = d.getSeconds()
      // console.log(this)
      // MM = this.addZero(MM)
      // dd = this.addZero(dd)
      // mm = this.addZero(mm)
      // ss = this.addZero(ss)
      return (yyyy + '年' + MM + '月' + dd + '日 ' + hh + ':' + mm + ':' + ss)
    }
  },
  methods: {
    showList () {
      // 如果本地有数据就读取本地，反则创建新的。
      let note = localStorage.getItem('localNote')
      if (note) {
        this.noteList = JSON.parse(note)
      } else {
        this.noteList = [{
          title: 'day1',
          content: 'Try to create a new note!',
          timeStamp: +new Date(),
          color: '#000',
          background: '#cdfbcd'
        }]
      };
    },
    addZero (e) {
      return e < 10 ? '0' + e : e
    },
    // 弹出便签
    showNote () {
      this.isShow = !this.isShow
      this.isRotate = !this.isRotate
    },
    // 改变便签的背景色
    changeColor (event) {
      if (event.target.tagName === 'SPAN') {
        let bgColor = event.target.attributes[0].value
        this.noteColor = bgColor
      };
    },
    // 改变便签的背景色
    changeBgColor (event) {
      if (event.target.tagName === 'SPAN') {
        let bgColor = event.target.attributes[0].value
        this.noteBgColor = bgColor
      };
    },
    // 创建便签
    createNote () {
      let now = +new Date()
      // 创建便签
      this.noteList.push({
        title: this.noteTitle,
        content: this.noteContent,
        timeStamp: now,
        color: this.noteColor,
        background: this.noteBgColor
      })
      // 清空，关闭创建便签的模板
      this.noteTitle = ''
      this.noteContent = ''
      this.noteColor = ''
      this.noteBgColor = ''
      this.isShow = !this.isShow
      this.isRotate = !this.isRotate
      // 保存本地
      this.saveLocal()
    },
    // delete note
    deleteNote (item) {
      let index = this.noteList.indexOf(item)
      this.noteList.splice(index, 1)
      // 保存本地
      this.saveLocal()
    },
    saveLocal () {
      // 保存数据到本地
      let localNote = JSON.stringify(this.noteList)
      localStorage.setItem('localNote', localNote)
    },
    saveNote (item, event) {
      let index = this.noteList.indexOf(item)
      // 获取标题和内容,文字以及背景色
      let tit = event.target.attributes[0].value
      let con = event.target.attributes[1].value
      let color = event.target.attributes[2].value
      let bgcolor = event.target.attributes[3].value
      let obj = this.noteList[index]
      obj.title = tit
      obj.content = con
      obj.timeStamp = +new Date()
      obj.color = color
      obj.background = bgcolor
      // 保存本地
      this.saveLocal()
    }
  }
}
</script>
