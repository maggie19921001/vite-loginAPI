<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <header>
    <div class="container">
      <h1>To Do List</h1>
      <h3>登入使用個人備忘清單</h3>
      <nav>
        <RouterLink to="/signin">註冊</RouterLink>
        <RouterLink to="/login">登入</RouterLink>
      </nav>
    </div>
  </header>

  <RouterView />
</template>

<style scoped>

</style>
