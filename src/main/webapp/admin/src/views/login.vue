<template>
  <div>
    <div class="container" :style='{"minHeight":"100vh","alignItems":"center","background":"url(http://codegen.caihongy.cn/20230831/14552f0035244c0392b42406c14a0e3d.jpg)","display":"flex","width":"100%","backgroundSize":"cover","backgroundPosition":"center center","backgroundRepeat":"no-repeat","justifyContent":"flex-start"}'>
      <el-form :style='{"border":"0px solid #eee","minHeight":"850px","padding":"60px 6%  160px","boxShadow":"0 0px 0px rgba(64, 158, 255, .6)","margin":"0 0 0 46%","borderRadius":"0px","textAlign":"center","background":"rgba(255,255,255,1)","width":"750px","fontSize":"16px","height":"auto"}'>
        <div v-if="true" :style='{"padding":"0px","margin":"20px auto 30px","color":"rgba(55,55,55,1)","textAlign":"left","background":"none","display":"inline-block","width":"100%","lineHeight":"40px","fontSize":"30px","fontWeight":"500"}' class="title-container">基于B/S结构的水电管理系统设计与实现登录</div>
        <div v-if="loginType==1" class="list-item" :style='{"width":"100%","margin":"0 auto 60px","position":"relative","alignItems":"center","flexWrap":"wrap","display":"flex"}'>
          <div v-if="false" class="lable" :style='{"color":"#666","left":"-150px","textAlign":"right","width":"150px","lineHeight":"44px","fontSize":"inherit","position":"absolute"}'>用户名：</div>
          <input :style='{"padding":"0 10px","borderColor":"#e6e6e6","color":"#666","borderRadius":"0px","background":"none","borderWidth":"0 0 1px","width":"480px","fontSize":"inherit","borderStyle":"solid","height":"40px"}' placeholder="请输入用户名" name="username" type="text" v-model="rulesForm.username">
        </div>
        <div v-if="loginType==1" class="list-item" :style='{"width":"100%","margin":"0 auto 60px","position":"relative","alignItems":"center","flexWrap":"wrap","display":"flex"}'>
          <div v-if="false" class="lable" :style='{"color":"#666","left":"-150px","textAlign":"right","width":"150px","lineHeight":"44px","fontSize":"inherit","position":"absolute"}'>密码：</div>
          <input :style='{"padding":"0 10px","borderColor":"#e6e6e6","color":"#666","borderRadius":"0px","background":"none","borderWidth":"0 0 1px","width":"480px","fontSize":"inherit","borderStyle":"solid","height":"40px"}' placeholder="请输入密码" name="password" type="password" v-model="rulesForm.password">
        </div>

		<div class="list-item select" :style='{"width":"100%","margin":"0 auto 60px","position":"relative","alignItems":"center","flexWrap":"wrap","display":"flex"}' v-if="roles.length>1">
		  <el-select v-model="rulesForm.role" placeholder="请选择角色">
		    <el-option v-for="item in roles" :key="item.roleName" :label="item.roleName" :value="item.roleName" />
		  </el-select>
		</div>

		
        <div :style='{"margin":"60px auto 0","alignItems":"center","flexWrap":"wrap","background":"none","display":"flex","width":"100%","fontSize":"16px","position":"relative","justifyContent":"flex-start"}'>
          <el-button v-if="loginType==1" :style='{"border":"0px solid #2a2d2e","cursor":"pointer","padding":"20px 20px 20px 30px","boxShadow":"inset 0px 0px 0px 0px rgba(19,161,230,.1)","margin":"0px","color":"#fff","textAlign":"center","letterSpacing":"10px","minWidth":"180px","right":"0","outline":"none","borderRadius":"0px","top":"100px","background":"#c8b670","width":"auto","fontSize":"24px","position":"absolute","fontWeight":"500","height":"auto"}' type="primary" @click="login()" class="loginInBt">登录</el-button>
          <el-button :style='{"border":"0px solid rgba(64, 158, 255, 1)","cursor":"pointer","padding":"0 8px","margin":"0 5px 5px","borderColor":"#72bc78","color":"#666","outline":"none","borderRadius":"0px","background":"none","borderWidth":"0 0 0 0px","width":"auto","fontSize":"inherit","borderStyle":"solid","fontWeight":"500","height":"auto"}' type="primary" @click="register('xuesheng')" class="register">注册学生</el-button>
        </div>
      </el-form>

    </div>
  </div>
</template>
<script>
import menu from "@/utils/menu";
export default {
  data() {
    return {
		verifyCheck2: false,
		flag: false,
      baseUrl:this.$base.url,
      loginType: 1,
      rulesForm: {
        username: "",
        password: "",
        role: "",
      },
      menus: [],
      roles: [],
      tableName: "",
    };
  },
  mounted() {
    let menus = menu.list();
    this.menus = menus;

    for (let i = 0; i < this.menus.length; i++) {
      if (this.menus[i].hasBackLogin=='是') {
        this.roles.push(this.menus[i])
      }
    }

  },
  created() {

  },
  destroyed() {
	    },
  components: {
  },
  methods: {

    //注册
    register(tableName){
		this.$storage.set("loginTable", tableName);
		this.$router.push({path:'/register',query:{pageFlag:'register'}})
    },
    // 登陆
    login() {

		if (!this.rulesForm.username) {
			this.$message.error("请输入用户名");
			return;
		}
		if (!this.rulesForm.password) {
			this.$message.error("请输入密码");
			return;
		}
		if(this.roles.length>1) {
			if (!this.rulesForm.role) {
				this.$message.error("请选择角色");
				return;
			}

			let menus = this.menus;
			for (let i = 0; i < menus.length; i++) {
				if (menus[i].roleName == this.rulesForm.role) {
					this.tableName = menus[i].tableName;
				}
			}
		} else {
			this.tableName = this.roles[0].tableName;
			this.rulesForm.role = this.roles[0].roleName;
		}
		
		this.loginPost()
    },
	loginPost() {
		this.$http({
			url: `${this.tableName}/login?username=${this.rulesForm.username}&password=${this.rulesForm.password}`,
			method: "post"
		}).then(({ data }) => {
			if (data && data.code === 0) {
				this.$storage.set("Token", data.token);
				this.$storage.set("role", this.rulesForm.role);
				this.$storage.set("sessionTable", this.tableName);
				this.$storage.set("adminName", this.rulesForm.username);
				this.$router.replace({ path: "/" });
			} else {
				this.$message.error(data.msg);
			}
		});
	},
  }
}
</script>

<style lang="scss" scoped>
.container {
  min-height: 100vh;
  position: relative;
  background-repeat: no-repeat;
  background-position: center center;
  background-size: cover;
      background: url(http://codegen.caihongy.cn/20230831/14552f0035244c0392b42406c14a0e3d.jpg);
        
  .list-item /deep/ .el-input .el-input__inner {
		border-radius: 0px;
		padding: 0 10px;
		color: #666;
		background: none;
		width: 480px;
		font-size: inherit;
		border-color: #e6e6e6;
		border-width: 0 0 1px;
		border-style: solid;
		height: 40px;
	  }
  
  .list-item.select /deep/ .el-select .el-input__inner {
		border-radius: 0px;
		padding: 0 10px;
		color: #666;
		width: 480px;
		font-size: 14px;
		border-color: #e6e6e6;
		border-width: 0 0 1px;
		border-style: solid;
		height: 44px;
	  }
  
  .list-code /deep/ .el-input .el-input__inner {
  	  	border-radius: 0px 0 0 0px;
  	  	padding: 0 10px;
  	  	outline: none;
  	  	color: #666;
  	  	background: none;
  	  	width: 400px;
  	  	font-size: inherit;
  	  	border-color: #e6e6e6;
  	  	border-width: 0 0 1px;
  	  	border-style: solid;
  	  	height: 40px;
  	  }

  .list-type /deep/ .el-radio__input .el-radio__inner {
		background: rgba(53, 53, 53, 0);
		border-color: #999;
	  }
  .list-type /deep/ .el-radio__input.is-checked .el-radio__inner {
        background: #c8b670;
        border-color: #c8b670;
      }
  .list-type /deep/ .el-radio__label {
		color: #666666;
		font-size: 16px;
	  }
  .list-type /deep/ .el-radio__input.is-checked+.el-radio__label {
        color: rgba(50,50,50,1);
        font-size: 16px;
      }
}

</style>
