<template>
  <div class="main">
    <Form
      id="formLogin"
      class="user-layout-login"
      ref="formLogin"
      :form="form"
      @submit="handleSubmit"
    >
      <Tabs
        :activeKey="customActiveKey"
        :tabBarStyle="{ textAlign: 'center', borderBottom: 'unset' }"
        @change="handleTabClick"
      >
        <TabPane key="tab1" tab="账号密码登录">
          <Alert v-if="isLoginError" type="error" showIcon style="margin-bottom: 24px;" message="账户或密码错误（admin/ant.design )" />
          <FormItem>
            <Input
              size="large"
              type="text"
              placeholder="账户"
              v-decorator="[
                'username',
                {rules: [{ required: true, message: '请输入帐户名或邮箱地址' }, { validator: handleUsernameOrEmail }], validateTrigger: 'change'}
              ]"
            >
              <Icon slot="prefix" type="user" :style="{ color: 'rgba(0,0,0,.25)' }"/>
            </Input>
          </FormItem>

          <FormItem>
            <Input
              size="large"
              type="password"
              autocomplete="false"
              placeholder="密码"
              v-decorator="[
                'password',
                {rules: [{ required: true, message: '请输入密码' }], validateTrigger: 'blur'}
              ]"
            >
              <Icon slot="prefix" type="lock" :style="{ color: 'rgba(0,0,0,.25)' }"/>
            </Input>
          </FormItem>
        </TabPane>
        <TabPane key="tab2" tab="手机号登录">
          <FormItem>
            <Input size="large" type="text" placeholder="手机号" v-decorator="['mobile', {rules: [{ required: true, pattern: /^1[34578]\d{9}$/, message: '请输入正确的手机号' }], validateTrigger: 'change'}]">
              <Icon slot="prefix" type="mobile" :style="{ color: 'rgba(0,0,0,.25)' }"/>
            </Input>
          </FormItem>

          <Row :gutter="16">
            <Col class="gutter-row" :span="16">
              <FormItem>
                <Input size="large" type="text" placeholder="验证码" v-decorator="['captcha', {rules: [{ required: true, message: '请输入验证码' }], validateTrigger: 'blur'}]">
                  <Icon slot="prefix" type="mail" :style="{ color: 'rgba(0,0,0,.25)' }"/>
                </Input>
              </FormItem>
            </Col>
            <Col class="gutter-row" :span="8">
              <Button
                class="getCaptcha"
                tabindex="-1"
                :disabled="state.smsSendBtn"
                @click.stop.prevent="getCaptcha"
                v-text="!state.smsSendBtn && '获取验证码' || (state.time+' s')"
              ></Button>
            </Col>
          </Row>
        </TabPane>
      </Tabs>

      <FormItem>
        <Checkbox v-decorator="['rememberMe', { valuePropName: 'checked' }]">自动登录</Checkbox>
        <router-link
          :to="{ name: 'recover', params: { user: 'aaa'} }"
          class="forge-password"
          style="float: right;">
          忘记密码
        </router-link>
      </FormItem>

      <FormItem style="margin-top:24px">
        <Button
          size="large"
          type="primary"
          htmlType="submit"
          class="login-button"
          :loading="state.loginBtn"
          :disabled="state.loginBtn">
          确定
        </Button>
      </FormItem>

      <div class="user-login-other">
        <span>其他登录方式</span>
        <a>
          <Icon class="item-icon" type="alipay-circle"></Icon>
        </a>
        <a>
          <Icon class="item-icon" type="taobao-circle"></Icon>
        </a>
        <a>
          <Icon class="item-icon" type="weibo-circle"></Icon>
        </a>
        <router-link class="register" :to="{ name: 'register' }">注册账户</router-link>
      </div>
    </Form>
  </div>
</template>

<script>
import { Form, Button, Checkbox, Col, Input, Tabs, Alert, Icon, Row } from 'ant-design-vue';

export default {
  components: {
      Form, Button,
      Checkbox, Col, Input,
      Tabs, Alert, Icon, Row,
      FormItem: Form.Item,
      TabPane: Tabs.TabPane
  },
  data () {
    return {
      customActiveKey: 'tab1',
      loginBtn: false,
      // login type: 0 email, 1 username, 2 telephone
      loginType: 0,
      isLoginError: false,
      requiredTwoStepCaptcha: false,
      stepCaptchaVisible: false,
      form: Form.createForm(this),
      state: {
        time: 60,
        loginBtn: false,
        // login type: 0 email, 1 username, 2 telephone
        loginType: 0,
        smsSendBtn: false
      }
    }
  },
  created () {
  },
  methods: {
    // handler
    handleUsernameOrEmail (rule, value, callback) {
    },
    handleTabClick (key) {
        this.customActiveKey = key
        // this.form.resetFields()
    },
    handleSubmit (e) {
    },
    getCaptcha (e) {
    },
    stepCaptchaSuccess () {
      this.loginSuccess()
    },
    stepCaptchaCancel () {
      this.Logout().then(() => {
        this.loginBtn = false
        this.stepCaptchaVisible = false
      })
    },
    loginSuccess (res) {
    },
    requestFailed (err) {
      this.isLoginError = true
      this.$notification['error']({
        message: '错误',
        description: ((err.response || {}).data || {}).message || '请求出现错误，请稍后再试',
        duration: 4
      })
    }
  }
}
</script>

<style lang="less" scoped>
.user-layout-login {
  label {
    font-size: 14px;
  }

  .getCaptcha {
    display: block;
    width: 100%;
    height: 40px;
  }

  .forge-password {
    font-size: 14px;
  }

  button.login-button {
    padding: 0 15px;
    font-size: 16px;
    height: 40px;
    width: 100%;
  }

  .user-login-other {
    text-align: left;
    margin-top: 24px;
    line-height: 22px;

    .item-icon {
      font-size: 24px;
      color: rgba(0, 0, 0, 0.2);
      margin-left: 16px;
      vertical-align: middle;
      cursor: pointer;
      transition: color 0.3s;

      &:hover {
        color: #1890ff;
      }
    }

    .register {
      float: right;
    }
  }
}
</style>
