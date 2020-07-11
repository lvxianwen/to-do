<template>
	<view class="content">
		<view class="todo-header" v-if="list.length !== 0">
			<view class="todo-header_left">
				<text class="active_text">{{text}}</text>
				<text>{{listData.length}}条</text>
			</view>
			<view class="todo-header_right">
				<view class="todo-header_right-item" @click="tab(0)" :class="{'active-tab':activeIndex === 0}">
					全部
				</view>
				<view class="todo-header_right-item" @click="tab(1)" :class="{'active-tab':activeIndex === 1}">
					待办
				</view>
				<view class="todo-header_right-item" @click="tab(2)" :class="{'active-tab':activeIndex === 2}">
					已完成
				</view>
			</view>
		</view>
		<view v-if="list.length === 0" class="default">
			<view class="image-default">
				<image src="../../static/default.png" mode="aspectFit"></image>
			</view>
			<view class="default-info">
				<view class="default-info-text">
					您还没有创建任何待办事项
				</view>
				<view class="default-info-text">
					点击下方+号创建一个吧
				</view>
			</view>
		</view>
		<view v-else class="todo-content">
			<view class="todo-list" :class="{'todo-finish':item.state}" v-for="(item,index) in listData" :key="index" @click="finish(index)">
				<view class="todo-list_checkbox">
					<view class="checkbox"></view>
				</view>
				<view class="todo-list_content">
					{{item.content}}
				</view>
			</view>
		</view>
		<view class="create-content" v-if="active" :class="{'create-show':textShow}">
			<view class="create-content-box">
				<view class="create-input">
					<input type="text"  v-model="value" placeholder="请输入您要创建的todo"/>
				</view>
				<view class="create-button" @click="create()">
					创建
				</view>
			</view>
		</view>
		<view class="create-todo" @click="add()">
			<text class="iconfont icon-add" :class="{'create-todo-active':textShow}"></text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				value:'',
				list:[
					{content:'1',state:false},
					{content:'2',state:false},
					{content:'3',state:true},
				],
				active:false,
				activeIndex:0,
				text:'全部',
				textShow:false
			}
		},
		onLoad() {

		},
		computed:{
			listData(){
				let list =JSON.parse(JSON.stringify(this.list))
				let newList = []
				
				//点击全部
				if(this.activeIndex === 0){
					this.text = '全部'
					return list
				}
				
				//点击待办事项
				if(this.activeIndex === 1){
					this.text ='待办'
					list.forEach((item)=>{
						if(!item.state){
							newList.push(item)
						}
					})
					return newList
				}
				
				//点击已完成
				if(this.activeIndex === 2){
					this.text = '完成'
					list.forEach((item)=>{
						if(item.state){
							newList.push(item)
						}
					})
					return newList
				}
				
			}
		},
		methods: {
			finish(n){
				this.list[n].state = !this.list[n].state
				console.log(this.list[n].state)
			},
			add(){
				if(this.active){
					this.close()
				}else{
					this.open()
				}
			},
			open(){
				this.active = true
				this.$nextTick(()=>{
					setTimeout(()=>{
						this.textShow = true
					},50)
				})
			},
			close(){
				this.textShow = false
				this.$nextTick(()=>{
					setTimeout(()=>{
						this.active = false
					},350)
				})
			},
			//创建按钮
			create(){
				console.log(this.value)
				if(this.value === ''){
					uni.showToast({
						title:'请输入内容',
						icon:'none'
					})
					return
				}
				this.list.unshift({
					content:this.value,
					state:false
				})
				this.value = ''
				this.active = false
			},
			tab(n){
				this.activeIndex = n
			}
		}
	}
</script>

<style>
	@import '../../common/icon.css';
	.icon {
	       width: 1em; height: 1em;
	       vertical-align: -0.15em;
	       fill: currentColor;
	       overflow: hidden;
	    }
	.todo-header{
		position: fixed;
		left: 0;
		top: 0;
		width: 100%;
		display: flex;
		align-items: center;
		font-size: 12px;
		color: #333;
		height: 45px;
		box-shadow: -1px 1px 5px 0 rgba(0,0,0,0.1);
		background-color: #fff;
		padding: 0 15px;
		box-sizing: border-box;
		z-index: 10;
	}
	
	.todo-header_left{
		width: 100%;
	}
	
	.active_text{
		font-size: 14px;
		color: #279ABF;
		padding-right: 10px;
	}
	
	.todo-header_right{
		flex-shrink: 0;
		display: flex;
	}
	
	.todo-header_right-item{
		padding:  0 5px;
	}
	
	.active-tab{
		color: #279abf;
	}
	
	.todo-content{
		position: relative;
		padding-top: 50px;
		padding-bottom: 100px;
	}
	
	.todo-list{
		position: relative;
		display: flex;
		align-items: center;
		padding: 15px;
		margin: 15px;
		color: #0c3854;
		font-size: 14px;
		background-color: #cfebfd;
		border-radius: 10px;
		box-shadow: -1px 1px 5px 1px rgba(0,0,0,0.1),-1px 2px 1px 0 rgba(255,255,255) inset;
	}
	
	.todo-list::after{
		position: absolute;
		content: '';
		top: 0;
		bottom: 0;
		left: 0;
		width: 5px;
		background-color: #91d1e8;
	}
	
	.todo-list_checkbox{
		padding-right: 15px;
	}
	
	.checkbox{
		width: 20px;
		height: 20px;
		border-radius: 50%;
		background-color: #fff;
		box-shadow: 0 0 5px 1px rgba(0,0,0,0.1);
	}
	
	.todo-finish .checkbox{
		position: relative;
		background-color: #eee;
	}
	
	.todo-finish .checkbox::after{
		position: absolute;
		width: 10px;
		height: 10px;
		content: '';
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		margin: auto;
		background-color: #cfebfd;
		border-radius: 50%;
	}
	
	.todo-finish .todo-list_content{
		color: #999;
	}
	
	.todo-finish .todo-list_content::after{
		content: '';
		position: absolute;
		top: 0;
		bottom: 0;
		left: 40px;
		right: 10px;
		height: 2px;
		margin: auto 0;
		background-color: #bdcdd8;
	}
	
	.todo-finish::after{
		background-color: #ccc;
	}
	
	.create-todo{
		display: flex;
		position: fixed;
		justify-content: center;
		align-items: center;
		bottom: 20px;
		left: 0;
		right: 0;
		margin: 0 auto;
		width: 50px;
		height: 50px;
		border-radius: 50%;
		background-color: #deeff5;
		box-shadow: -1px 1px 5px 2px rgba(0,0,0,0.1);
	}
	
	.icon-add{
		font-size: 30px;
		color: #add8e6;
	}
	
	.create-content{
		position: fixed;
		bottom: 95px;
		left: 20px;
		right: 20px;
		transition: all 0.3s;
		opacity: 0;
		transform: scale(0) translateY(200%);
	}
	
	.create-show{
		opacity: 1;
		transform: scale(1) translateY(0);
	}
	
	.create-content-box{
		display: flex;
		align-items: center;
		padding: 0 15px;
		padding-right: 0;
		border-radius: 50px;
		background-color: #fff;
		box-shadow: -1px 1px 5px 2px rgba(0,0,0,0.1);
		background-color: #DEEFF5;
		z-index: 2;
	}
	
	.create-input{
		width: 100%;
		padding-left: 15px;
		color: #ADD8E6;
	}
	
	.create-button{
		display: flex;
		justify-content: center;
		align-items: center;
		flex-shrink: 0;
		width: 80px;
		height: 50px;
		font-size: 16px;
		border-radius: 50px;
		color: #88d4ec;
		box-shadow: -2px 0px 2px 1px rgba(0,0,0,0.1);
	}
	
	.create-content::after{
		content: '';
		position: absolute;
		right: 0;
		left: 0;
		bottom: -8px;
		margin:  0 auto;
		width: 20px;
		height: 20px;
		background-color: #DEEFF5;
		transform: rotate(45deg);
		box-shadow: 1px 1px 5px 2px rgba(0,0,0,0.1);
		z-index: -1;
	}
	
	.create-content-box::after{
		content: '';
		position: absolute;
		right: 0;
		left: 0;
		bottom: -8px;
		margin:  0 auto;
		width: 20px;
		height: 20px;
		background-color: #DEEFF5;
		transform: rotate(45deg);
	}
	
	.default{
		padding-top: 100px;
	}
	
	.image-default{
		display: flex;
		justify-content: center;
	}
	
	.image-default image{
		width: 100%;
	}
	
	.default-info{
		text-align: center;
		font-size: 14px;
		color: #ccc;
	}
	
	.icon-add{
		transition: transform 0.3s;
	}
	
	.create-todo-active{
		transform: rotate(135deg);
	}
	
	
	
	

	
	
</style>
