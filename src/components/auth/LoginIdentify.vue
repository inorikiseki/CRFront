<template>
  <div>
    <el-card class="login-card">
      <h2 class="login-title">用户登录</h2>
      <el-form
          :model="loginForm"
          label-width="60px"
          label-position="left"
          class="login-form"
      >
        <el-row>
          <el-col :span="24">
            <el-form-item label="账号" prop="username">
              <el-input v-model="loginForm.username" placeholder="请输入账号"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="24">
            <el-form-item label="密码" prop="password">
              <el-input type="password" v-model="loginForm.password" placeholder="请输入密码"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="14">
            <el-form-item label="验证码" prop="captcha">
              <el-input v-model="loginForm.captcha" placeholder="请输入验证码" maxlength="5"
                        @keyup.enter="handleLoginSubmit"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="9">
            <div class="right-content">
              <captcha-input></captcha-input>
            </div>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="18">
            <div class="left-content">
              <router-link to="/auth/reset" class="link" @mouseover="hoverLink('forgotPassword', true)"
                           @mouseleave="hoverLink('forgotPassword', false)">
                忘记密码?
              </router-link>
              <span class="divider">|</span>
              <router-link to="/auth/register" class="link" @mouseover="hoverLink('register', true)"
                           @mouseleave="hoverLink('register', false)">
                马上注册
              </router-link>
            </div>
          </el-col>
          <el-col :span="6">
            <div class="right-content">
              <el-button type="primary" @click="handleLoginSubmit">{{ $t('auth.login') }}</el-button>
            </div>
          </el-col>
        </el-row>
      </el-form>
    </el-card>
  </div>
</template>

<script setup>
import {reactive} from 'vue';
import CaptchaInput from './CaptchaInput.vue'
import {ElMessage} from 'element-plus'
import axios from 'axios';
import {useRouter} from 'vue-router'
import {useStore} from 'vuex'
import {serialize} from "object-to-formdata";

const store = useStore()
const loginForm = reactive({
  username: '',
  password: '',
  captcha: ''
});

const emit = defineEmits(['refreshData'])

const router = useRouter()
//验证基础的字段是否为空
const validateEmpty = () => {
  if (loginForm.username.trim() === '') {
    ElMessage.error('请输入账号');
    return false;
  }
  if (loginForm.password.trim() === '') {
    ElMessage.error('请输入密码');
    return false;
  }
  if (loginForm.captcha.trim() === '') {
    ElMessage.error('请输入验证码');
    return false;
  }
  return true;
};
const handleLoginSubmit = () => {

  if (!validateEmpty()) {
    return; // 如果有字段为空，则直接返回，不执行后续逻辑
  }

  const renamedLoginData = {
    accountNum: loginForm.username,
    password: loginForm.password,
    verify: loginForm.captcha
  }

  fetchUserInfo(renamedLoginData)
  router.push("/home")

};

const hoverLink = (linkName, isHover) => {
  const link = document.querySelector(`.${linkName}`);
  if (isHover) {
    link.style.color = '#1890ff'; // 悬浮时的颜色
    link.style.textDecoration = 'underline'; // 悬浮时的下划线
  } else {
    link.style.color = ''; // 恢复默认颜色
    link.style.textDecoration = ''; // 恢复默认下划线
  }
};

const fetchUserInfo = (query_data) => {
  axios.post("/my_chatroom/user/login", query_data, {
    headers: {
      'Content-Type': 'multipart/form-data'
    }
  })
      .then(res => {
        let tem = res.data.code
        if (tem === 1 || tem === 3) {
          ElMessage.error('账号或密码错误');
        } else if (tem === 2) {
          ElMessage.error('该账号已被封禁');
        } else if (tem === 4) {
          ElMessage.error('验证码错误');
        } else if (tem === 200) {
          ElMessage.success('登录成功');
          store.dispatch('myinfos/updateMyInfos', res.data.data)
          console.log(store.state.myinfos.myinfos)
        }
      })
      .catch(err => {
        console.error(err); // 打印错误信息
      });
}

</script>

<style scoped>

.login-card {
  width: 400px;
  padding: 20px;
}

.login-title {
  text-align: center;
  margin-bottom: 20px;
}

.login-form {
  max-width: 300px;
  margin: 0 auto;
}

/* 栅格布局调整 */
.el-row {
  display: flex;
  justify-content: space-between;
}

.el-col {
  margin-bottom: 10px;
}

.divider {
  margin: 0 5px;
}

.link {
  cursor: pointer;
}

.link:hover {
  color: #e3ba4c; /* 悬浮时的颜色 */
  text-decoration: underline; /* 悬浮时的下划线 */
}

.left-content {
  text-align: left;
}

.right-content {
  text-align: right;
}
</style>
