<template>
  <div class="login-container">
    <div class="warp">
     <div class="imgButtons">
      <h1 style="color:#fff"><a target="_blank" href="https://github.com/wmhello/laravel_template_with_vue" alt="跳转到开源项目">
        <svg-icon icon-class="github" /><span>开源项目</span>
      </a></h1>
      <h1 style="color:#fff"><a :href="docAddress" target="_blank">
        <svg-icon icon-class="article" /><span>文档</span>
      </a></h1>
    </div>
      <div class="loginForm">
        <h3 class="title">系统登录</h3>
       <el-form ref="loginForm" :model="loginForm" :rules="loginRules" class="login-form" auto-complete="on" label-position="left">
          <el-form-item prop="username">
            <span class="svg-container"><svg-icon icon-class="user" /></span>
            <el-input ref="username" v-model="loginForm.username" placeholder="登录名" name="username" type="text" tabindex="1" auto-complete="on" />
          </el-form-item>

          <el-form-item prop="password">
            <span class="svg-container"><svg-icon icon-class="password" /></span>
            <el-input
              :key="passwordType"
              ref="password"
              v-model="loginForm.password"
              :type="passwordType"
              placeholder="密码"
              name="password"
              tabindex="2"
              auto-complete="on"
              @keyup.enter.native="handleLogin"
            />
            <span class="show-pwd" @click="showPwd"><svg-icon :icon-class="passwordType === 'password' ? 'eye' : 'eye-open'" /></span>
          </el-form-item>

          <el-button :loading="loading" type="primary" style="width:100%;margin-bottom:30px;" @click.native.prevent="handleLogin">登录</el-button>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data() {
    return {
      loginForm: {
        username: '',
        password: ''
      },
      loginRules: {
        username: [{ required: true, trigger: 'blur', message: '登陆名必须填写' }, { min: 3, trigger: 'blur', message: '登陆名不少于三个字符' }],
        password: [{ required: true, trigger: 'blur', message: '登陆密码必须填写' }, { min: 3, trigger: 'blur', message: '登陆密码不少于6个字符' }]
      },
      loading: false,
      passwordType: 'password',
      redirect: undefined
    };
  },
  watch: {
    $route: {
      handler: function(route) {
        this.redirect = route.query && route.query.redirect;
      },
      immediate: true
    }
  },
  methods: {
    showPwd() {
      if (this.passwordType === 'password') {
        this.passwordType = '';
      } else {
        this.passwordType = 'password';
      }
      this.$nextTick(() => {
        this.$refs.password.focus();
      });
    },
    handleLogin() {
      this.$refs.loginForm.validate(valid => {
        if (valid) {
          this.loading = true;
          this.$store
            .dispatch('user/login', this.loginForm)
            .then(() => {
              this.$router.push({ path: this.redirect || '/' });
              this.loading = false;
            })
            .catch(error => {
              let result = error.response.data;
              this.$message.error(result.info);
              this.loading = false;
              this.loginForm = {
                username: '',
                password: ''
              };
            });
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    }
  },
  computed: {
    docAddress() {
      let hostURL = process.env.VUE_APP_BASE_API;
      return hostURL + 'showdoc/web/#/1';
    }
  }
};
</script>

<style lang="scss" scoped>
.login-container {
  overflow: hidden;
  position: relative;
  width: 100vw;
  height: 100vh;
  .loginForm {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 620px;
    height: 380px;
    transform: translate(-50%, -50%);
    .login-form {
      width: 60%;
      margin: 0 auto;
    }
  }
}
.warp{
  position: absolute;
  top: 0px;
  left: 0px;
  right: 0px;
  bottom: 0px;
  background-color:rgba(133,133,133,0.4);
}
</style>
<style lang="scss">
	/* 修复input 背景不协调 和光标变色 */
	/* Detail see https://github.com/PanJiaChen/vue-element-admin/pull/927 */

	$bg:#283443;
	$light_gray:#fff;
  $dark_gray: #000;
	$cursor: #fff;

	@supports (-webkit-mask: none) and (not (cater-color: $cursor)) {
		.login-container .el-input input {
			color: $cursor;
		}
	}

	/* reset element-ui css */
	.login-container {
		.el-input {
			display: inline-block;
			height: 47px;
			width: 85%;

			input {
				background: transparent;
				border: 0px;
				-webkit-appearance: none;
				border-radius: 0px;
				padding: 12px 5px 12px 15px;
				color: $light_gray;
				height: 47px;
				caret-color: $cursor;

				&:-webkit-autofill {
					box-shadow: 0 0 0px 1000px $bg inset !important;
					-webkit-text-fill-color: $cursor !important;
				}
			}
		}

		.el-form-item {
			border: 1px solid rgba(255, 255, 255, 0.1);
			background: rgba(0, 0, 0, 0.1);
			border-radius: 5px;
			color: #454545;
		}

    .login-form {
       position: relative;
       width: 520px;
       max-width: 100%;
       padding: 160px 35px 0;
       margin: 0 auto;
       overflow: hidden;
     }

     .tips {
       font-size: 14px;
       color: #fff;
       margin-bottom: 10px;

       span {
         &:first-of-type {
           margin-right: 16px;
         }
       }
     }

     .svg-container {
       padding: 6px 5px 6px 15px;
       color: $dark_gray;
       vertical-align: middle;
       width: 30px;
       display: inline-block;
     }

     .title-container {
       position: relative;

       .title {
         font-size: 26px;
         color: $light_gray;
         margin: 0px auto 40px auto;
         text-align: center;
         font-weight: bold;
       }
     }

     .show-pwd {
       position: absolute;
       right: 10px;
       top: 7px;
       font-size: 16px;
       color: $dark_gray;
       cursor: pointer;
       user-select: none;
     }
     & .imgButtons{
       position: fixed;
       right: 15px;
       & h1{
         display:inline-block;
         margin-right: 20px;
         position:relative;
         width:60px;
         & a:link span{
            display:none;
          }
         & a:hover span{
           display:inline;
           font-weight: normal;
           position: absolute;
           top: 50px;
           left: 1px;
           font-size:12px;
         }
       }
     }
	}

</style>
