<template>
  <div id="app">
    <div id="navbar" class="navbar-collapse">
      <ul class="navbar-nav">
        <li><router-link to="/">主页</router-link></li>
        <li><router-link to="/slide">左右滑动</router-link></li>
      </ul>
    </div>
    <router-view></router-view>
  </div>
</template>

<script>

export default {
  name: 'App'
}
</script>

<style>
  .navbar-collapse {
    width: 500px;
    height: 50px;
  }
  .navbar-nav {
    list-style: none;
  }
  .navbar-nav li {
    float: left;
    padding: 0px 10px;
  }
</style>
