<template>
  <div class="tg-container">
    <!-- <div v-loading='loading' style='height: 100%;' element-loading-text='登录中...' /> -->
    <p class="tips">
      loading...
    </p>
    <p class="tips">
      使用该功能需要“科学上网”
    </p>
  </div>
</template>

<script>
import axios from 'axios'
const crypto = require('crypto')
const oauth = require('oauth')

export default {
  layout: 'empty',
  data() {
    return {
      token: {}
    }
  },
  async mounted() {
    // const res2 = await this.$API.twitterUrl({})
    // const step1 = (await axios({
    //   method: 'get',
    //   url: 'https://twitter.com/oauth/request_token?oauth_consumer_key=psCq7zJc8a9UyNOX5etVSuKVh&oauth_nonce=3h4p6ebn5ask&oauth_signature_method=HMAC-SHA1&oauth_version=1.0&oauth_signature=kIBLltY7zcRxMnYaeeDkiBEJliU%3Doauth_timestamp=1582271893'
    // })).data
    // console.log(step1)
    // console.log(res2)
    // this.ready()
    this.oauthlogin()
  },
  methods: {
    randomString(len) {
      len = len || 32
      const $chars = 'abcdefhijkmnprstwxyz2345678'
      const maxPos = $chars.length
      let pwd = ''
      for (let i = 0; i < len; i++) {
        pwd += $chars.charAt(Math.floor(Math.random() * maxPos))
      }
      return pwd
    },
    async oauthlogin(){
      console.log( process.env.VUE_APP_URL + '/login/twitter_logined')
      var _twitterConsumerKey = 'LdQ6moi0sfGzsPBtVMxjPhbeq'
      var _twitterConsumerSecret = '98txEZ4xf6Y2bNxgEDRRJ02Jyipn0Lq1xazQMyYyCmJNwdO04t'
      var consumer = new oauth.OAuth(
        'https://twitter.com/oauth/request_token', 'https://twitter.com/oauth/access_token', 
        _twitterConsumerKey, _twitterConsumerSecret, '1.0A', 'https://test.smartsignature.io/login/twitter_logined/', 'HMAC-SHA1')
        consumer.getOAuthRequestToken(function(error, oauthToken, oauthTokenSecret, results){
        if (error) {
          console.log(error)
          // res.send('Error getting OAuth request token : ' + util.inspect(error), 500)
        } else {  
          // req.session.oauthRequestToken = oauthToken
          // req.session.oauthRequestTokenSecret = oauthTokenSecret
          console.log('https://twitter.com/oauth/authorize?oauth_token=' + oauthRequestToken)
          // res.redirect('https://twitter.com/oauth/authorize?oauth_token='+req.session.oauthRequestToken)  
        }
      })
    },
    async ready() {
      const timestamp = parseInt(Date.parse(new Date())) / 1000
      const ranstr = this.randomString(12)
      const httpmethod = 'POST'
      const twitterurl = 'https://api.twitter.com/oauth/request_token'
      const params = 
      // 'oauth_callback=' + process.env.VUE_APP_URL + '/login/twitter_logined/' +
      'oauth_consumer_key=' + process.env.TWITTER_APP_KEY +
      '&oauth_nonce=' + ranstr +
      '&oauth_signature_method=' + 'HMAC-SHA1' +
      '&oauth_timestamp=' + timestamp +
      '&oauth_version=' + '1.0'
      const signtext = httpmethod + '&' + encodeURIComponent(twitterurl) + '&' + encodeURIComponent(params)
      const signkey = encodeURIComponent(process.env.TWITTER_APP_KEY) + '&'
      const sign = encodeURIComponent(crypto.createHmac('sha1', signkey).update(signtext).digest().toString('base64'))
      console.log(sign)
      const requesturl = 'https://api.twitter.com/oauth/request_token' // 
      // const requesturl = 'https://twitter.com/oauth/request_token?' +
      // 'oauth_consumer_key=' + process.env.TWITTER_APP_KEY +
      // '&oauth_nonce=' + ranstr +
      // '&oauth_signature_method=' + 'HMAC-SHA1' +
      // '&oauth_version=' + '1.0' +
      // '&oauth_signature=' + sign +
      // 'oauth_timestamp=' + timestamp
      console.log(requesturl)
      console.log(process.env.VUE_APP_URL + '/login/twitter_logined')
      const Authorization = 'OAuth ' + 'oauth_nonce="' + ranstr + '", ' +
        // 'oauth_callback="' + encodeURIComponent(process.env.VUE_APP_URL + '/login/twitter_logined/') + '", ' +
        'oauth_signature_method="HMAC-SHA1", ' +
        'oauth_consumer_key="' + process.env.TWITTER_APP_KEY + '", ' +
        'oauth_timestamp="' + timestamp + '", ' +
        'oauth_signature="' + sign + '", ' +
        'oauth_version="1.0"'
      console.log(Authorization)
      try {
        const step1 = (await axios({
          method: 'post',
          url: requesturl,
          headers: {
            'Authorization': Authorization
          },
        }))
        console.log(step1)
        const token = {}
        const step1arr = step1.split('&')
        for (const index in step1arr) {
          token[step1arr[index].split('=')[0]] = step1arr[index].split('=')[1]
        }
        this.token = token
        // window.location = 'https://api.twitter.com/oauth/authenticate?oauth_token=' + token.oauth_token
      } catch (error) {
        // this.$router.go(-1)
        console.log(error)
        this.$message.closeAll()
        this.$message.error(error.toString())
      }
    }
  }
}
</script>

<style scoped lang='less'>
.tg-container {
  display: flex;
  flex-direction: column;
  align-content: center;
  align-items: center;
  padding-top: 10%;
  .tips {
    font-size: 14px;
    color: #333;
    padding: 0;
    margin: 2em 0 0;
  }
}
</style>
