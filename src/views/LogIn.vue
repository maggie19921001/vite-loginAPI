<template>
  <div class="container">
    <h3>登入</h3>
    <h5>如無帳號請先註冊</h5>
    <p>帳號</p>
    <input type="email" placeholder="Email" v-model="LoginData.email">
    <p>密碼</p>
    <input type="password" placeholder="Password" v-model="LoginData.password">
    <button type="button" @click="Login">確定</button>
    <div v-if="Token">
      <p>點選驗證以繼續</p>
    </div> 
    <div v-else>
      <p>{{ FillError }}</p>
    </div>
    <h3>驗證資訊</h3>
    <div v-if="user.uid">
      <p>驗證成功</p>
      <p>UID:{{ user.uid }}</p>
      <p>Nickname:{{ user.nickname }}</p>
      <RouterLink to="/todolist">我的代辦清單</RouterLink>
    </div>
    <div v-else>
      <div v-if="checkError">
        <p>{{ checkError }}</p>
      </div>
      <div v-else>
        <p>Token</p>
      </div>
    </div>
    <input type="text" v-model="Token">
    <button type="button" @click="checkOut">驗證</button>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";
import axios from "axios";

const ApiBase = "https://todolist-api.hexschool.io";

//登入
const LoginData = ref({
  email: "",
  password: "",
});
const Token = ref('')
const FillError = ref('')

const Login = async()=>{
  try{
    const res = await axios.post(`${ApiBase}/users/sign_in`,LoginData.value);
    alert("登入成功")
    Token.value = res.data.token;

  }catch(error){
    FillError.value =error.message;
  }
}

//驗證 
const user = ref({
  nickname:'',
  uid:''
})
const checkError = ref('');

const checkOut = async()=>{
  const LimitDate = new Date(); //設定當前日期 如8/10
  LimitDate.setDate(LimitDate.getDate() + 1); //設定日期為>取得日10+1=8/11
  document.cookie = `loginToken=${Token.value}; expires=${LimitDate.toUTCString()}`; 
  //cookie值設定為輸入之token，取得日期後轉為時間字串格式設定為過期日
  try{
    const res = await axios.get(`${ApiBase}/users/checkout`,{
    headers:{Authorization:Token.value,}
    });
    user.value=res.data
  }catch(error){
    checkError.value =error.message;
  }
}

</script>

<style>
.container{
  display: flex;
  flex-direction: column;
  padding: 20px;
  max-width: 500px;
  word-wrap:break-word
}
button{
  margin-top: 10px;
}
</style>
