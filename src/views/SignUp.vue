<template>
  <div class="container">
    <h3>註冊</h3>
    <p>名稱</p>
    <input type="text" placeholder="Nickname" v-model="SignUpData.nickname">
    <p>帳號</p>
    <input type="email" placeholder="Email" v-model="SignUpData.email">
    <p>密碼(至少六個字)</p>
    <input type="password" placeholder="Password" v-model="SignUpData.password">
    <button type="button" @click="SignUp">確定</button>
    ID:{{ ID }}
    {{ FillError }}
</div>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

const ApiBase = "https://todolist-api.hexschool.io";
const SignUpData = ref({
  email: "",
  password: "",
  nickname: ""
});
const ID = ref('') //先有空值才能存取接收值，並使用在畫面上
const FillError = ref('')

async function SignUp(){
  try{
    const res = await axios.post(`${ApiBase}/users/sign_up`,SignUpData.value);
    alert("註冊成功")
    ID.value = res.data.uid;

  }catch(error){
    FillError.value =error.message;
  }
  
}
</script>

<style>
.container{
  display: flex;
  flex-direction: column;
  padding: 20px;
  max-width: 500px;
}
button{
  margin-top: 10px;
}

</style>
