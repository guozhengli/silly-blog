<style lang="less">
  @import './login.less';
</style>

<template>
  <div class="login">
    <div class="login-con">
      <Card icon="log-in" title="欢迎登录" :bordered="false">
        <div class="form-con">
          <login-form :loading="loading" @on-success-valid="handleSubmit"></login-form>
        </div>
      </Card>
    </div>
  </div>
</template>

<script>
import LoginForm from '_c/login-form'
import { mapActions } from 'vuex'

export default {
  components: {
    LoginForm
  },
  data () {
    return {
      loading: false
    }
  },
  methods: {
    ...mapActions([
      'handleLogin',
      'getUserInfo'
    ]),
    handleSubmit ({ userName, password }) {
      this.loading = true
      this.handleLogin({ userName, password }).then(res => {
        this.loading = false
        this.getUserInfo().then(res => {
          this.$router.push({
            name: this.$route.query.next || 'home'
          })
        })
      }).catch(err => {
        this.loading = false
        const data = err.response.data
        const message = data.error.message.toLowerCase()
        if (message.includes('password')) {
          this.$Message.error('密码错误')
        } else if (message.includes('username')) {
          this.$Message.error('账号错误')
        } else {
          this.$Message.error(message)
        }
      })
    },
    /* 管理后台的body样式和游客前台的不一样, 需要进行切换 */
    toggleBodyClass (add) {
      if (add) {
        document.documentElement.classList.add('manage-app')
        document.body.classList.add('manage-app')
      } else {
        document.documentElement.classList.remove('manage-app')
        document.body.classList.remove('manage-app')
      }
    }
  },
  mounted () {
    this.toggleBodyClass(true)
  },
  destroyed () {
    this.toggleBodyClass(false)
  }
}
</script>

<style>

</style>
