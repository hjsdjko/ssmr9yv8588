<template>
	<div class="navbar">
		<div class="title" :style='{"display":"block","order":"1"}'>
			<el-image v-if="false" class="title-img" :style='{"width":"44px","objectFit":"cover","borderRadius":"100%","float":"left","height":"44px"}' src="http://codegen.caihongy.cn/20201114/7856ba26477849ea828f481fa2773a95.jpg" fit="cover" />
			<span class="title-name" :style='{"padding":"0 0 0 12px","color":"#fff","background":"none","lineHeight":"44px","fontSize":"28px","float":"left","fontWeight":"500"}'>{{this.$project.projectName}}</span>
		</div>
		<!--
		<div class="right" :style='{"position":"absolute","right":"20px","top":"8px","display":"flex"}'>
			<div :style='{"cursor":"pointer","margin":"0 5px","lineHeight":"44px","color":"#333"}' class="nickname">{{this.$storage.get('role')}} {{this.$storage.get('adminName')}}</div>
			<div :style='{"cursor":"pointer","margin":"0 5px","lineHeight":"44px","color":"#666"}' class="logout" @click="onLogout">退出登录</div>
		</div>
		-->
		
		<el-dropdown @command="handleCommand" trigger="click" :style='{"fontSize":"inherit","position":"absolute","right":"30px","color":"inherit","display":"inline-block"}'>
		  <div class="el-dropdown-link" :style='{"alignItems":"center","display":"none"}'>
		    <el-image v-if="user" :style='{"width":"32px","margin":"0 10px","objectFit":"cover","borderRadius":"100%","display":"inline-block","height":"32px"}' :src="avatar?this.$base.url + avatar : require('@/assets/img/avator.png')" fit="cover"></el-image>
		    <span :style='{"color":"inherit","lineHeight":"32px","fontSize":"inherit"}'>{{this.$storage.get('adminName')}}</span>
		    <span class="icon iconfont icon-xiala" :style='{"color":"inherit","margin":"0 0 0 5px","fontSize":"14px"}'></span>
		  </div>
		  <div class="top-el-dropdown-menu-2" :style='{"alignItems":"center","justifyContent":"center","display":"flex"}'>
		    <div class="item1" @click="handleCommand('')">首页</div>
		    <div class="item2" @click="handleCommand('center')">个人信息</div>
		    <div class="item4" @click="handleCommand('logout')">退出</div>
		  </div>
		</el-dropdown>
		
	</div>
</template>

<script>
	import axios from 'axios'
	export default {
		data() {
			return {
				dialogVisible: false,
				ruleForm: {},
				user: null,
			};
		},
		created() {
		},
		computed: {
			avatar(){
				return this.$storage.get('headportrait')?this.$storage.get('headportrait'):''
			}
		},
		mounted() {
			let sessionTable = this.$storage.get("sessionTable")
			this.$http({
				url: sessionTable + '/session',
				method: "get"
			}).then(({
				data
			}) => {
				if (data && data.code === 0) {
					if(sessionTable == 'xuesheng') {
						this.$storage.set('headportrait',data.data.touxiang)
					}
					if(sessionTable == 'users') {
						this.$storage.set('headportrait',data.data.image)
					}
					this.$storage.set('userForm',JSON.stringify(data.data))
					this.user = data.data;
					this.$storage.set('userid',data.data.id);
				} else {
					let message = this.$message
					message.error(data.msg);
				}
			});
		},
		methods: {
			handleCommand(name) {
				if (name == 'front') {
					this.onIndexTap()
				} else if (name == 'logout') {
					this.onLogout()
				} else if (name == 'board'){
					this.toBoard()
				} else if (name == 'backUp'){
					this.backUp()
				} else {
					let router = this.$router
					name = '/'+name
					router.push(name)
				}
			},
			
			onLogout() {
				let storage = this.$storage
				let router = this.$router
				storage.clear()
				this.$store.dispatch('tagsView/delAllViews')
				router.replace({
					name: "login"
				});
			},
			onIndexTap(){
				window.location.href = `${this.$base.indexUrl}`
			},
		}
	};
</script>


<style lang="scss" scoped>
	.top-el-dropdown-menu-2 div.item1 {
				padding: 0 3px;
				color: #666;
				display: none;
				font-size: 14px;
				line-height: 32px;
			}

	.top-el-dropdown-menu-2 div.item1:hover {
				color: red;
			}
	
	.top-el-dropdown-menu-2 div.item2 {
				cursor: pointer;
				padding: 0 3px;
				color: inherit;
				font-size: inherit;
				line-height: 32px;
			}
	
	.top-el-dropdown-menu-2 div.item2:hover {
				color: #ff0;
			}
	
	.top-el-dropdown-menu-2 div.item3 {
				cursor: pointer;
				padding: 0 3px;
				color: inherit;
				font-size: inherit;
				line-height: 32px;
			}
	
	.top-el-dropdown-menu-2 div.item3:hover {
				color: #ff0;
			}
	
	.top-el-dropdown-menu-2 div.item4 {
				cursor: pointer;
				padding: 0 3px;
				color: inherit;
				font-size: inherit;
				line-height: 32px;
			}
	
	.top-el-dropdown-menu-2 div.item4:hover {
				color: #ff0;
			}
</style>
