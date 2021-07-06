<template>
    <div class="login-container">
        <van-nav-bar class="page-nav-bar" title="登录">
            <van-icon slot="left" name="cross" @click="$router.back()"></van-icon>
        </van-nav-bar>
        <van-form ref="loginRef" @submit="onSubmit">
            <van-field v-model="user.mobile" name="mobile" placeholder="请输入手机号" :rules="userFormRules.mobile" type="number" maxlength="11">
                <i slot="left-icon" class="toutiao toutiao-shouji"></i>
            </van-field>
            <van-field left-icon="" v-model="user.code" name="code" placeholder="请输入验证码" :rules="userFormRules.code" type="number" maxlength="6">
                <i slot="left-icon" class="toutiao toutiao-yanzhengma"></i>
                <template #button>
                    <van-count-down :time="1000 * 30" format="ss s" v-if="isCountDownShow" @finish="isCountDownShow = false"></van-count-down>
                    <van-button v-else native-type="button" class="send-sms-btn" round size="small" type="default" @click="sendSms">发送验证码</van-button>
                </template>
            </van-field>
            <div class="login-btn-wrap">
                <van-button class="login-btn" block type="info" native-type="submit">登录</van-button>
            </div>
        </van-form>
    </div>
</template>

<script>
import { login, sendSms } from '../../api/user'

export default {
  name: 'LoginIndex',
  components: {},
  props: {},
  data () {
    return {
      user: {
        mobile: '',
        code: ''
      },
      isCountDownShow: false,
      userFormRules: {
        mobile: [
          {
            required: true,
            message: '请填写手机号'
          },
          {
            patten: /^1[3|5|7|8|9]\d{9}$/,
            message: '请填写正确的手机号',
            trigger: 'blur'
          }
        ],
        code: [
          {
            required: true,
            message: '请填写验证码'
          },
          {
            patten: /^\d{6}$/,
            message: '验证码格式错误'
          }
        ]
      }
    }
  },
  computed: {},
  watch: {},
  created () {},
  mounted () {},
  methods: {
    async onSubmit () {
      const user = this.user
      this.$toast.loading({
        message: '登录中...',
        forbidClick: true,
        duration: 0
      })
      try {
        const { data } = await login(user)
        this.$store.commit('setUser', data.data)
        this.$toast.success('登录成功')
        this.$router.back()
      } catch (err) {
        if (err.response.status === 400) {
          this.$toast.fail('手机号或验证码错误')
        } else {
          this.$toast.fail('登录失败')
        }
      }
    },
    async sendSms () {
      try {
        await this.$refs.loginRef.validate('mobile')
      } catch (err) {
        return console.log('f')
      }

      this.isCountDownShow = true
      try {
        await sendSms(this.user.mobile)
        this.$toast('发送成功')
      } catch (err) {
        this.isCountDownShow = false
        if (err.response.status === 429) {
          this.$toast('发送太频繁了')
        }
        this.$toast('发送失败')
      }
    }
  }
}
</script>

<style lang="less" scoped>
.login-container {
  .toutiao {
    font-size: 37px;
  }
  .send-sms-btn {
    background-color: #ededed;
    width: 152px;
    height: 46px;
    line-height: 46px;
    font-size: 22px;
    color: #666;
  }

  .login-btn-wrap {
    padding: 53px 33px;
    .login-btn {
      background-color: #6db4fb;
      border: none;
    }
  }
}
</style>
