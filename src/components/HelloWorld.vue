<template>
  <div class="hello">
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
  </div>
</template>

<script>
import SMSClient from '../lib/aliyun'
export default {
  name: 'HelloWorld',
  data () {
    return {
      sendDatas: [
        {
          phoneNumber: '',
          name: ''
        }
      ],
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
          TemplateCode: 'TemplateCode', //短信模版
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
