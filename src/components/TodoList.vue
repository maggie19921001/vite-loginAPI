<template>
     <div class="container">
        <p>Token: {{ tokenCookie }}</p>
        <button type="button" @click="logOut">登出</button>
        <h3>待辦清單</h3>
        <div class="new">
            <input type="text" placeholder="to do something" v-model="todoText">
            <button type="button" @click="todoAdd">確定</button>
        </div>
        <hr>
        <ul>
            <li v-for="(todo , index) in todos" :key="index">
                <input type="checkbox" v-model="todo.status" @click="toggleStatus(todo.id)">
                {{ todo.content }}   {{ todo.status?'完成':'未完成' }}
                <div v-if="todo.isEditing">
                    <input type="text" v-model="editText[todo.id]" @change="changeText($event, todo.id)">
                    <button type="button" @click="comfirmEdit(todo.id)">確定</button>
                </div>
                <button type="button" @click="todoEdit(todo.id)">編輯</button>
                <button type="button" @click="todoDelete(todo.id)">刪除</button>
            </li>
        </ul>
    </div>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
const ApiBase = "https://todolist-api.hexschool.io";
import { useRouter } from 'vue-router';

//功能設定：按編輯按鈕後。編輯按鈕消失，編輯框顯示（內有原始值）並出現確定按鈕
//按下確定後原始值更新，剩下編輯按鈕

// 從cookie中提取token (cookie原始值是物件)
const getTokenFromCookie = () => {
  const cookieArr = document.cookie.split(';');
  for (let cookie of cookieArr) {
    const [name, value] = cookie.trim().split('=');
    if (name === 'loginToken') {
      return value;
    }
  }
  return null;
};
const tokenCookie = getTokenFromCookie();

//登出 
const router = useRouter(); // 引入 useRouter Hook
//[?]因登出不需提供資料，所以第二個{}為空 (但不確定為何不能省略)
const logOut = async()=>{
    try{
        const res = await axios.post(`${ApiBase}/users/sign_out`,{},{
        headers:{Authorization:tokenCookie,}
        });
        router.push("/login");
    }catch(error){
        console.log(error.message);
    }
}
//資料設定
const todos =ref([]);//API回傳陣列
const todoText =ref('');//輸入值
const todo = {content:'',}//API傳輸值
const editText =ref({});//編輯值

// 取得所有待辦
const getTodos = async()=>{
    const res = await axios.get(`${ApiBase}/todos`,{
    headers:{Authorization:tokenCookie,}
    });
    console.log(res.data);
    todos.value=res.data.data;
}
getTodos();
// 新增 先取得content再發送post，最後還原空格
const todoAdd = async()=>{
    if(todoText != undefined){
        todo.content = todoText.value
    }
    await axios.post(`${ApiBase}/todos`,todo,{
        headers:{Authorization:tokenCookie,}
    })
    todoText.value='';
    getTodos();
}
//編輯
const changeText = (event,id) => {
    editText.value = {
    ...editText.value,
    [id]: event.target.value,
  };
};
const todoEdit = (id)=> {
    // 找到對應的 todo 項目，切換 isEditing 狀態
    const todo = todos.value.find((todo) => todo.id === id);
    if (todo) {
        todo.isEditing = !todo.isEditing;//將原先false轉為true
        editText.value[id] = todo.content;
    }
}
const comfirmEdit = async(id)=>{
    const todo = todos.value.find((todo) => todo.id === id);
    todo.content = editText.value[id];
    await axios.put(`${ApiBase}/todos/${id}`,todo, {
        headers: {Authorization: tokenCookie,},
    });

    todo.isEditing = false; // 隱藏編輯框
    getTodos();
     editText.value = {
    ...editText.value,
    [id]: '',
}
}
//刪除
const todoDelete = async(id)=>{
    if (confirm("確定要刪除嗎？") == true) {
        await axios.delete(`${ApiBase}/todos/${id}`, {
        headers: {Authorization:tokenCookie,},
      });
     getTodos();
    }else {
};

}
//完成狀態
const toggleStatus = async (id) => {
  await axios.patch(
    `${ApiBase}/todos/${id}/toggle`,
    {},
    {headers: { Authorization: tokenCookie,},}
  );
  getTodos();
};
</script>

<style>
.container{
  display: flex;
  flex-direction: column;
  padding: 20px;
  max-width: 500px;
  word-wrap:break-word
}
.new{
    width: 500px;
}
button{
  margin-top: 10px;
  max-width: 50px;
  align-self: flex-end;
}
ul{
    list-style-type:none;
}
</style>