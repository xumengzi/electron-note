<template>
  <div id="myNote">
    <h2>{{message}}</h2>
    <h4>{{tips}}</h4>
    <ul class="notes">
      <li v-for="item in noteList" :key="item">
          <div v-bind:style="{background: item.background,color: item.color}">
              <span class="time">{{item.timeStamp | timeFormat}}</span>
              <div v-bind:style="{borderColor: item.color}" class="title">
                <input maxlength="15" v-model="item.title" 
                  v-bind:value="item.title" />
              </div>
              <div class="content">
                <textarea v-model="item.content"></textarea>		
              </div>
          </div>
          <span v-bind:tit="item.title" v-bind:con="item.content" v-bind:color="item.color" v-bind:bgcolor="item.background" v-on:click="saveNote(item,event)" class="saveNote"></span>
          <span v-on:click="deleteNote(item)" class="deleteNote"></span>
      </li>
    </ul>
    <span v-on:click="showNote" class="addNew" v-bind:class="{addRotate: isRotate}"></span>
    <div class="newNote" v-bind:style="{background: noteBgColor,color: noteColor}"  v-bind:class="{showNote: isShow}">
      <div v-on:click="changeColor" class="customized color">
        <label class="description">文字</label><span color="#f60" class="selected"></span><span color="#000"></span><span color="#eee4c9"></span><span color="#ffa"></span>
      </div>
      <div v-on:click="changeBgColor" class="customized bgcolor">
        <label class="description">背景色</label><span color="#cdfbcd" class="selected"></span><span color="#fcc"></span><span color="#eee4c9"></span><span color="#ffa"></span>
      </div>
      <div class="content">
        <input type="text" v-model="noteTitle" placeholder="这里是便签标题">
        <textarea placeholder="这里是便签内容" v-model="noteContent"></textarea>
        <span class="create" v-on:click="createNote">create</span>
      </div>
    </div>
  </div>
</template>

<script>
import './note.css'
export default {
  data: {
		message: '这是一个在线便签',
		tips: '预防颈椎，从我做起',
		noteList: [],
		isShow: false,
		isRotate: false,
		// 便签内容
		noteTitle: '',
		noteContent: '',
		noteColor: '',
		noteBgColor: ''
	},
	//初始化
	mounted: function(){
		this.$nextTick(function(){
			//展示便签列表
			this.showList();
		})
	},
	filters: {
		//格式化时间
		timeFormat: function(value){
			let d = new Date(value),
				yyyy = d.getFullYear(),
				MM = d.getMonth() + 1,
				dd = d.getDate(),
				hh = d.getHours(),
				mm = d.getMinutes(),
				ss = d.getSeconds();
			MM = vm.addZero(MM);
			dd = vm.addZero(dd);
			mm = vm.addZero(mm);
			ss = vm.addZero(ss);
			return (yyyy + '年' + MM + '月' + dd + '日 ' + hh + ':' + mm + ':' + ss);
		}
	},
	//一些方法
	methods: {
		//展示便签列表
		showList: function(){
			//如果本地有数据就读取本地，反则创建新的。
			let note = localStorage.getItem("localNote");
			if (note) {
				this.noteList = JSON.parse(note);
			}else{
				this.noteList = [{
          title: "day1",
          content: "Try to create a new note!",
          timeStamp: +new Date(),
          color: "#000",
          background: "#cdfbcd"
        }];
			};
		},
		editTitle: function(){},
		addZero: function(e){
			return e = (e < 10 ? '0' + e : e);
		},
		//弹出便签
		showNote: function(){
			this.isShow = !this.isShow;
			this.isRotate = !this.isRotate;
		},
		//改变便签的背景色
		changeColor: function(event){
			if (event.target.tagName == "SPAN") {
				let bgColor = event.target.attributes[0].value;
				this.noteColor = bgColor;
			};
		},
		//改变便签的背景色
		changeBgColor: function(event){
			if (event.target.tagName == "SPAN") {
				let bgColor = event.target.attributes[0].value;
				this.noteBgColor = bgColor;
			};
		},
		//创建便签
		createNote: function(){
			let now = + new Date();
			//创建便签
			this.noteList.push({
				title: this.noteTitle,
				content: this.noteContent,
				timeStamp: now,
				color: this.noteColor,
				background: this.noteBgColor
			});
			//清空，关闭创建便签的模板
			this.noteTitle = '';
			this.noteContent = '';
			this.noteColor = '';
			this.noteBgColor = '';
			this.isShow = !this.isShow;
			this.isRotate = !this.isRotate;
			//保存本地
			this.saveLocal();
		},
		//delete note
		deleteNote: function(item){
			let index = this.noteList.indexOf(item);
			this.noteList.splice(index,1);
			//保存本地
			this.saveLocal();
		},
		saveLocal: function(){
			//保存数据到本地
			let localNote = JSON.stringify(this.noteList);
			localStorage.setItem("localNote",localNote);
		},
		saveNote: function(item,event){
			let index = this.noteList.indexOf(item);
			//获取标题和内容,文字以及背景色
			let tit = event.target.attributes[0].value,
			    con = event.target.attributes[1].value,
			    color = event.target.attributes[2].value,
			    bgcolor = event.target.attributes[3].value;
			let obj = this.noteList[index];
			obj.title = tit;
			obj.content = con;
			obj.timeStamp = +new Date();
			obj.color = color;
			obj.background = bgcolor;
			//保存本地
			this.saveLocal();
		}
	}
}
</script>