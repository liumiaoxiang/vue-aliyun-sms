<template>
  <div class="hello">
  <div v-if="!isLogin">
     <div>
        用户名：<input v-model="username"  >
      </div>
      <div>
        密&nbsp;&nbsp;&nbsp;&nbsp;码：<input v-model="password" >
      </div>
      <button @click="login"> 登录</button>
  </div>
  <div v-else>
    <template v-for="item in sendDatas">
      <div>
        电话号码：<input v-model="item.phoneNumber"  >
      </div>
      <div>
        联系姓名：<input v-model="item.name" >
      </div>
      <hr />
    </template>
    <div>
      工作人员：<input v-model="worker" >
    </div>
    <button @click="addData"> 添加人员</button>
    <button @click="sendMsg"> 发送短信</button>
    <button @click="loginout"> 退出登录</button>

  </div>
  </div>
</template>

<script>
import SMSClient from '../lib/aliyun'
import crypto from 'crypto'
export default {
  name: 'Index',
  data () {
    return {
      sendDatas: [
        {
          phoneNumber: '',
          name: ''
        }
      ],
      isLogin: false,
      username: '',
      password: '',
      pwd: '22CCECC67E538A0C88365C00735B0BD0',
      phoneNumber: '',
      smsClient: null,
      accessKeyId: 'accessKeyId', // 阿里云短信accessKeyId
      secretAccessKey: 'secretAccessKey', // 阿里云短信secretAccessKey
      worker: '',
      name: ''
    }
  },
  mounted () {
    // const SMSClient = require('../lib/aliyun')
    let accessKeyId = this.accessKeyId
    let secretAccessKey = this.secretAccessKey
    this.smsClient = new SMSClient({ accessKeyId, secretAccessKey })
    if (window.sessionStorage.getItem('pwd') === this.pwd) {
      this.isLogin = true
    }
  },
  methods: {
    addData () {
      this.sendDatas.push({
        phoneNumber: '',
        name: ''
      })
    },
    sendMsg () {
      if (!this.worker) return
      this.sendDatas.forEach(item => {
        if (!item.phoneNumber || !item.name) return
        let data = {
          PhoneNumbers: `${item.phoneNumber}`, // 电话号码
          SignName: 'SignName', // 阿里云签名
          TemplateCode: 'TemplateCode', // 短信模版
          TemplateParam: `{"customer":"123456"}` // 模版内容替换
        }
        console.log(data)
        this.smsClient.sendSMS(data).then(function (res) {
          let {Code} = res
          if (Code === 'OK') {
            // 处理返回参数
            console.log(res)
          }
        }, function (err) {
          console.log(err)
        })
      })
    },
    login () {
      let md5 = crypto.createHash('md5')
      md5.update(this.password)
      let pwd = md5.digest('hex').toUpperCase()
      if (this.username === 'admin' && pwd === this.pwd) {
        this.isLogin = true
        window.sessionStorage.setItem('pwd', pwd)
      } else {
        alert('用户名或者密码错误！')
      }
    },
    loginout () {
      this.isLogin = false
      window.sessionStorage.clear()
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style  scoped>
.hello div{
  padding: 8px;
}
.hello input{
  height: 20px;
  width: 300px;
}
.hello button{
  height: 40px;
  border-radius: 8px
}
</style>
