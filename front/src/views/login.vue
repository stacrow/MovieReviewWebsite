<template>
  <div>
    <div class="container loginIn">
      <div class="login-container">
        <div class="login-img">
          <img src="@/assets/img/bg_purple.jpg" alt="login">
        </div>
        <div class="login-form-container">
          <el-form class="login-form" label-position="left" :label-width="0">
            <div class="title-container"><h3 class="title" style="color: #34017b">电影评论网站系统</h3></div>
            <el-form-item :class="'style'+2">
              <el-input placeholder="请输入用户名" name="username" type="text" v-model="rulesForm.username" />
            </el-form-item>
            <el-form-item :class="'style'+2">
              <el-input placeholder="请输入密码" name="password" type="password" v-model="rulesForm.password" />
            </el-form-item>
            <el-form-item v-if="0 == '1'" class="code" :class="'style'+2">
              <el-input placeholder="请输入验证码" name="code" type="text" v-model="rulesForm.code" />
              <div class="getCodeBt" @click="getRandCode(4)" style="height:44px;line-height:44px">
                <span v-for="(item, index) in codes" :key="index" :style="{color:item.color,transform:item.rotate,fontSize:item.size}">{{ item.num }}</span>
              </div>
            </el-form-item>
            <el-form-item class="role">
              <el-radio
                v-for="item in menus"
                v-if="item.hasBackLogin=='是'"
                v-bind:key="item.roleName"
                v-model="rulesForm.role"
                :label="item.roleName"
              >{{item.roleName}}</el-radio>
            </el-form-item>
            <el-button type="primary" @click="login()" class="loginInBt" style="padding:0;font-size:16px;border-radius:4px;height:44px;line-height:44px;width:100%;backgroundColor:#34017b; borderColor:#34017b; color:rgba(255, 255, 255, 1)">{{'1' == '1' ? '登录' : 'login'}}</el-button>
            <el-form-item class="setting">
              <!-- <div style="color:#34017b" class="reset">修改密码</div> -->
            </el-form-item>
          </el-form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import menu from "@/utils/menu";
export default {
  data() {
    return {
      rulesForm: {
        username: "",
        password: "",
        role: "",
        code: '',
      },
      menus: [],
      tableName: "",
      codes: [{
        num: 1,
        color: '#000',
        rotate: '10deg',
        size: '16px'
      },{
        num: 2,
        color: '#000',
        rotate: '10deg',
        size: '16px'
      },{
        num: 3,
        color: '#000',
        rotate: '10deg',
        size: '16px'
      },{
        num: 4,
        color: '#000',
        rotate: '10deg',
        size: '16px'
      }],
    };
  },
  mounted() {
    let menus = menu.list();
    this.menus = menus;
  },
  created() {
    this.setInputColor()
    this.getRandCode()
  },
  methods: {
    setInputColor(){
      this.$nextTick(()=>{
        document.querySelectorAll('.loginIn .el-input__inner').forEach(el=>{
          el.style.backgroundColor = "rgba(247, 247, 247, 1)"
          el.style.color = "rgba(51, 51, 51, 1)"
          el.style.height = "44px"
          el.style.lineHeight = "44px"
          el.style.borderRadius = "4px"
        })
        document.querySelectorAll('.loginIn .style3 .el-form-item__label').forEach(el=>{
          el.style.height = "44px"
          el.style.lineHeight = "44px"
        })
        document.querySelectorAll('.loginIn .el-form-item__label').forEach(el=>{
          el.style.color = "#34017b"
        })
        setTimeout(()=>{
          document.querySelectorAll('.loginIn .role .el-radio__label').forEach(el=>{
            el.style.color = "#34017b"
          })
        },350)
      })

    },
    register(tableName){
      this.$storage.set("loginTable", tableName);
      this.$router.push({path:'/register'})
    },
    // 登陆
    login() {
      let code = ''
      for(let i in this.codes) {
        code += this.codes[i].num
      }
	  if ('0' == '1' && !this.rulesForm.code) {
	     this.$message.error("请输入验证码");
	    return;
	  }
      if ('0' == '1' && this.rulesForm.code.toLowerCase() != code.toLowerCase()) {
         this.$message.error("验证码输入有误");
		this.getRandCode()
        return;
      }
      if (!this.rulesForm.username) {
         this.$message.error("请输入用户名");
        return;
      }
      if (!this.rulesForm.password) {
         this.$message.error("请输入密码");
        return;
      }
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
      this.$http({
        url: `${this.tableName}/login?username=${this.rulesForm.username}&password=${this.rulesForm.password}`,
        method: "post"
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.$storage.set("Token", data.token);
          this.$storage.set("role", this.rulesForm.role);
          this.$storage.set("sessionTable", this.tableName);
          this.$storage.set("adminName", this.rulesForm.username);
          this.$router.replace({ path: "/index/" });
        } else {
          this.$message.error(data.msg);
        }
      });
    },
    getRandCode(len = 4){
      this.randomString(len)
    },
    randomString(len = 4) {
      let chars = [
          "a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k",
          "l", "m", "n", "o", "p", "q", "r", "s", "t", "u", "v",
          "w", "x", "y", "z", "A", "B", "C", "D", "E", "F", "G",
          "H", "I", "J", "K", "L", "M", "N", "O", "P", "Q", "R",
          "S", "T", "U", "V", "W", "X", "Y", "Z", "0", "1", "2",
          "3", "4", "5", "6", "7", "8", "9"
      ]
      let colors = ["0", "1", "2","3", "4", "5", "6", "7", "8", "9", "a", "b", "c", "d", "e", "f"]
      let sizes = ['14', '15', '16', '17', '18']

      let output = [];
      for (let i = 0; i < len; i++) {
        // 随机验证码
        let key = Math.floor(Math.random()*chars.length)
        this.codes[i].num = chars[key]
        // 随机验证码颜色
        let code = '#'
        for (let j = 0; j < 6; j++) {
          let key = Math.floor(Math.random()*colors.length)
          code += colors[key]
        }
        this.codes[i].color = code
        // 随机验证码方向
        let rotate = Math.floor(Math.random()*60)
        let plus = Math.floor(Math.random()*2)
        if(plus == 1) rotate = '-'+rotate
        this.codes[i].rotate = 'rotate('+rotate+'deg)'
        // 随机验证码字体大小
        let size = Math.floor(Math.random()*sizes.length)
        this.codes[i].size = sizes[size]+'px'
      }
    },
  }
};
</script>

<style lang="scss" scoped>
.loginIn {
  min-height: 100vh;
  position: relative;
  background: linear-gradient(135deg, #e6e6fa 0%, #d8bfd8 100%);
  background-repeat: no-repeat;
  background-position: center center;
  background-size: cover;
  font-family: "PingFang SC", "Microsoft YaHei", "Source Han Sans", "Hiragino Sans GB", "SimHei", sans-serif;

  .login-container {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    width: 720px;
    height: 446px;
    display: flex;
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
  }

  .login-img {
    width: 360px;
    height: 446px;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: rgba(255, 255, 255, 0.2);
    
    img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      border-radius: 8px 0 0 8px;
    }
  }

  .login-form-container {
    width: 360px;
    height: 446px;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: rgba(255, 255, 255, 0.2);
    border-radius: 0 8px 8px 0;
  }

  .login-form {
    height: 100%;
    padding: 0 12px;
    box-sizing: border-box;
    display: flex;
    justify-content: center;
    flex-direction: column;
    background-color: transparent;
  }

  .title-container {
    text-align: center;
    font-size: 24px;
    font-weight: 500;

    .title {
      margin: 20px 0;
      letter-spacing: 1px;
    }
  }

  .el-form-item {
    position: relative;

    .el-input {
      display: inline-block;
      height: 40px;
      width: 100%;

      & ::v-deep input {
        background: transparent;
        border: 0px;
        -webkit-appearance: none;
        padding: 0 15px;
        color: #fff;
        height: 40px;
        font-family: "PingFang SC", "Microsoft YaHei", "Source Han Sans", "Hiragino Sans GB", "SimHei", sans-serif;
        font-size: 14px;
      }
    }
  }

  .code {
    .el-form-item__content {
      position: relative;

      .getCodeBt {
        position: absolute;
        right: 0;
        top: 0;
        line-height: 40px;
        width: 100px;
        background-color: rgba(51,51,51,0.4);
        color: #fff;
        text-align: center;
        border-radius: 0 4px 4px 0;
        height: 40px;
        overflow: hidden;

        span {
          padding: 0 5px;
          display: inline-block;
          font-size: 16px;
          font-weight: 600;
        }
      }

      .el-input {
        & ::v-deep input {
          padding: 0 130px 0 30px;
        }
      }
    }
  }

  .setting {
    & ::v-deep .el-form-item__content {
      padding: 0 15px;
      box-sizing: border-box;
      line-height: 32px;
      height: 32px;
      font-size: 14px;
      color: #999;
      margin: 0 !important;

      .register {
        float: left;
        width: 50%;
      }

      .reset {
        float: right;
        width: 50%;
        text-align: right;
      }
    }
  }

  .style2 {
    .el-input {
      & ::v-deep input {
        padding: 0 15px !important;
      }
    }
  }

  .code.style2, .code.style3 {
    .el-input {
      & ::v-deep input {
        padding: 0 115px 0 15px;
      }
    }
  }

  .style3 {
    & ::v-deep .el-form-item__label {
      padding-right: 6px;
    }

    .el-input {
      & ::v-deep input {
        padding: 0 15px !important;
      }
    }
  }

  .role {
    & ::v-deep .el-form-item__content {
      display: flex;
      justify-content: space-around;
      margin-left: 0 !important;
      height: 33px;
      line-height: 33px;
    }

    & ::v-deep .el-radio {
      margin-right: 0;
      flex: 1;
      text-align: center;
      height: 33px;
      line-height: 33px;

      .el-radio__input.is-checked .el-radio__inner {
        background-color: #34017b;
        border-color: #34017b;
      }

      .el-radio__input.is-checked + .el-radio__label {
        color: #34017b;
      }
    }

    & ::v-deep .el-radio__input {
      .el-radio__inner {
        border-radius: 50%;
      }
    }
  }
}

.el-form-item {
  .el-input {
    display: inline-block;
    height: 33px;
    width: 100%;

    & ::v-deep .el-input__inner {
      height: 33px;
      line-height: 33px;
      border-radius: 16px !important;
      background-color: rgba(255, 255, 255, 0.9);
    }
  }
}

.loginInBt {
  border-radius: 16px !important;
}
</style>
