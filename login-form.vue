<style lang='less' scoped>
  .login {
    width: 100%;
    height: 100%;
    background-image: url("../../assets/images/login-bg.jpg");
    background-size: cover;
    background-position: center;
    position: relative;
    &-con {
      position: absolute;
      right: 160px;
      top: 50%;
      transform: translateY(-60%);
      width: 300px;
      &-header {
        font-size: 16px;
        font-weight: 300;
        text-align: center;
        padding: 30px 0;
      }
      .form-con {
        padding: 10px 0 0;
      }
      .login-tip {
        font-size: 10px;
        text-align: center;
        color: #c3c3c3;
      }
    }
  }

  .loginRow {
    margin-top: 6%;
  }

  .loginImg {
    width: 180px;
    height: 40px;
    display: inline-block;
    margin-bottom: 10px;
  }

  .card {
    width: 380px;
    height: 450px;
    padding: 20px;
  }

  .card h4 {
    margin: 10px 0;
  }
  .goRegister {
    line-height: 60px;
  }
  .goRegister .ivu-btn {
    border: none;
    color: #00aaff;
    padding: 0;
  }

  .dynamicInput {
    width: 160px;
  }

  /*验证码部分s*/
  .verificationCode {
    width: 130px;
    height: 36px;
    position: relative;
    border: 1px solid #cccccc;
  }
  canvas {
    width: 100%;
    height: 100%;
  }
  #verifyCanvas{
    display: none;
  }
  #code_img {
    position: absolute;
    top: 0;
    width: 100%;
    height: 100%;
    cursor: pointer;
    vertical-align: top;
  }

  /*验证码部分e*/
</style>

<template>
  <Form ref="loginForm" :model="loginForm" :rules="rules" @keydown.enter.native="handleSubmit">
    <FormItem prop="userName">
      <Input clearable size="large" v-model="loginForm.userName" placeholder="请输入用户名">
      <Icon :size="20" type="ios-phone-portrait" slot="prefix"></Icon>
      </Input>
    </FormItem>
    <FormItem prop="password">
      <Input clearable type="password" size="large" v-model="loginForm.password" placeholder="请输入密码">
      <Icon type="md-lock" slot="prefix"/>
      </Input>
    </FormItem>
    <FormItem prop="dynamicCode">
      <Row type="flex" justify="space-between">
        <Input clearable class="dynamicInput" size="large" v-model="loginForm.dynamicCode" placeholder="请输入验证码">
        <Icon type="ios-person-outline" slot="prefix"></Icon>
        </Input>
        <div ref="verificationCode" class="verificationCode">
          <canvas width="130" height="36" id="verifyCanvas"></canvas>
          <img  @click="resetCode" id="code_img">
        </div>
      </Row>
    </FormItem>
    <Row type="flex" justify="center">
      <Button long size="large" @click="handleSubmit" type="primary">登录</Button>
    </Row>
    <Row class="goRegister">
      没有账号？
      <Button replace to="/register">免费注册</Button>
    </Row>
  </Form>
</template>
<script>
import $ from 'jquery'
export default {
  name: 'LoginForm',
  mounted () {
    this.drawCode(this.str)
  },
  props: {
    userNameRules: {
      type: Array,
      default: () => {
        return [
          { required: true, message: '请输入用户名', trigger: 'blur' }
        ]
      }
    },
    passwordRules: {
      type: Array,
      default: () => {
        return [
          { required: true, message: '请输入密码', trigger: 'blur' }
        ]
      }
    },
    dynamicCode: {
      type: Array,
      default: () => {
        return [
          { required: true, message: '请输入动态验证码', trigger: 'blur' }
        ]
      }
    }
  },
  data () {
    return {
      loginForm: {
        userName: 'super_admin',
        password: '',
        dynamicCode: 'xyzr'
      },
      nums: [
        '1', '2', '3', '4', '5', '6', '7', '8', '9', '0',
        'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L',
        'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z',
        'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l',
        'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z'
      ],
      str: ''
    }
  },
  computed: {
    rules () {
      return {
        userName: this.userNameRules,
        password: this.passwordRules,
        dynamicCode: this.dynamicCode
      }
    }
  },
  methods: {
    // 绘制验证码
    drawCode (str) {
      const canvas = document.getElementById('verifyCanvas') // 获取HTML端画布
      const context = canvas.getContext('2d') // 获取画布2D上下文
      context.fillStyle = '#FFF' // 画布填充色
      context.fillRect(0, 0, canvas.width, canvas.height) // 清空画布
      context.fillStyle = '#00aaff' // 设置字体颜色
      context.font = '25px Arial' // 设置字体
      const rand = []
      const x = []
      const y = []
      for (let i = 0; i < 4; i++) {
        rand.push(rand[i])
        rand[i] = this.nums[Math.floor(Math.random() * this.nums.length)]
        x[i] = i * 20 + 10
        y[i] = Math.random() * 20 + 20
        context.fillText(rand[i], x[i], y[i])
      }
      str = rand.join('').toUpperCase()
      // 画3条随机线
      for (let i = 0; i < 3; i++) {
        this.drawline(canvas, context)
      }
      // 画30个随机点
      for (let i = 0; i < 30; i++) {
        this.drawDot(canvas, context)
      }
      this.convertCanvasToImage(canvas)
      return str
    },
    // 随机线
    drawline (canvas, context) {
      // 随机线的起点x坐标是画布x坐标0位置，y坐标是画布高度的随机数
      context.moveTo(Math.floor(Math.random() * canvas.width), Math.floor(Math.random() * canvas.height))
      // 随机线的终点x坐标是画布宽度，y坐标是画布高度的随机数
      context.lineTo(Math.floor(Math.random() * canvas.width), Math.floor(Math.random() * canvas.height))
      context.lineWidth = 0.5 // 随机线宽
      context.strokeStyle = 'rgba(50,50,50,0.3)' // 随机线描边属性
      context.stroke() // 描边，即起点描到终点
    },
    // 随机点(所谓画点其实就是画1px像素的线，方法不再赘述)
    drawDot (canvas, context) {
      let px = Math.floor(Math.random() * canvas.width)
      let py = Math.floor(Math.random() * canvas.height)
      context.moveTo(px, py)
      context.lineTo(px + 1, py + 1)
      context.lineWidth = 0.2
      context.stroke()
    },
    // 绘制图片
    convertCanvasToImage (canvas) {
      document.getElementById('verifyCanvas').style.display = 'none'
      let image = document.getElementById('code_img')
      image.src = canvas.toDataURL('image/png')
      return image
    },
    resetCode () {
      this.drawCode()
      $('#verifyCanvas').remove()
      $('#code_img').before('<canvas width="130" height="36" id="verifyCanvas"></canvas>')
    },

    handleSubmit () {
      this.$refs.loginForm.validate((valid) => {
        if (valid) {
          this.$emit('on-success-valid', {
            userName: this.loginForm.userName,
            password: this.loginForm.password,
            dynamicCode: this.loginForm.dynamicCode
          })
        }
      })
    }
  }
}
</script>
