<template lang='pug'>
.home
  .login
    .form(@submit="submit")
      //- 帳號
      .form-group
        .form-label
          label(for='account') Account：
        input.form-control(type='text', v-model='account' name='account' placeholder='Account' autofocus=true)
      //- 密碼
      .form-group
        .form-label
          label.form-label(for='password') Password：
        input.form-control(type='password', v-model='password' placeholder='Password' name='{:password}')
      //- 記住帳號密碼
      .form-group
        .form-label
          .form-label Remember：
        input.form-control(type='checkbox' v-model='rememberPsw')
      //- 送出
      button.btn.btn-primary(type='submit' @click='submit') Submit
</template>
<script>
// @ is an alias to /src
import CryptoJS from 'crypto-js'
export default {
  data () {
    return {
      account: '',
      password: '',
      rememberPsw: false
    }
  },
  created () {
    // this.example()
    // this.setCookie('sosukeTv','sosukePsw', true, 30)
    // this.clearCookie()
    this.getCookie()
  },
  methods: {
    // 範例 - 加密 & 解密
    example () {
      // Encrypt 加密
      let ciphertext = CryptoJS.AES.encrypt('sosukeTv', 'secret key').toString()
      // Decrypt 解密
      let bytes = CryptoJS.AES.decrypt(ciphertext, 'secret key')
      // eslint-disable-next-line
      let decryptedData = bytes.toString(CryptoJS.enc.Utf8)
    },
    submit () {
      this.rememberPsw === true ? this.setCookie(this.account, this.password, 30) : this.clearCookie()

      // example login api
      // axios.login({
      //   account: this.account,
      //   password: this.password
      // }).then(res => {
      //   // success login
      //   if (this.rememberPsw === true) { this.setCookie(this.account, this.password, 30) }
      // }).catch(err => {
      //   // login error
      //   this.clearCookie()
      // })
    },
    // 設置cookie
    setCookie (account, psw, rememberPsw, exdays) {
      // Encrypt，加密賬號密碼
      var cipherAccount = CryptoJS.AES.encrypt(account, 'secretAccount').toString()
      var cipherPsw = CryptoJS.AES.encrypt(psw, 'secretPsw').toString()

      var exdate = new Date() // 獲取時間
      exdate.setTime(exdate.getTime() + 24 * 60 * 60 * 1000 * exdays) // 保存的天數
      // 字符串拼接cookie，為什麼這裡用了= =，因為加密後的字符串也有個=號，影響下面getcookie的字符串切割，你也可以使用更炫酷的符號。
      window.document.cookie = 'currentAccount' + '==' + cipherAccount + 'path=/expires=' + exdate.toGMTString()
      window.document.cookie = 'password' + '==' + cipherPsw + 'path=/expires=' + exdate.toGMTString()
      window.document.cookie = 'rememberPsw' + '==' + this.rememberPsw + '==' + exdate.toGMTString()
    },
    // 讀取cookie
    getCookie () {
      if (document.cookie.length > 0) {
        let arr = document.cookie.split('; ') // 這裡顯示的格式請根據自己的代碼更改
        for (let i = 0; i < arr.length; i++) {
          let arr2 = arr[i].split('==') // 根據==切割
          // 判斷查找相對應的值
          if (arr2[0] === 'currentAccount') {
            // Decrypt，將解密後的內容賦值給賬號
            let bytes = CryptoJS.AES.decrypt(arr2[1].slice(0, 44), 'secretAccount')
            this.account = bytes.toString(CryptoJS.enc.Utf8)
          } else if (arr2[0] === 'password') {
            // Decrypt，將解密後的內容賦值給密碼
            let bytes = CryptoJS.AES.decrypt(arr2[1].slice(0, 44), 'secretPsw')
            this.password = bytes.toString(CryptoJS.enc.Utf8)
          } else if (arr2[0] === 'rememberPsw') {
            arr2[1] === 'true' ? this.rememberPsw = true : this.rememberPsw = false
          }
        }
      }
    },
    // 清除cookie
    clearCookie () {
      this.setCookie('', '', false, -1)
    }
  }
}
</script>
<style lang="stylus">
.home
  display flex
  justify-content center
  align-items center
  width 100vw
  height 100vh
  .login
    width 375px
    height 300px
    background white
    .form
      padding 20px
      .form-group
        margin-bottom 20px
        .form-label
          width 100px
          display inline-block
          text-align right
        input
          padding-left 10px
</style>
