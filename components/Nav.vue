<template>
  <a-layout-header class="nav">
    <div class="logo" />
    <a-menu class="menu" theme="dark" mode="horizontal">
      <a-menu-item>
        <n-link to="/movies">
          Movies
        </n-link>
      </a-menu-item>
      <a-menu-item>
        <n-link to="/directors">
          Directors
        </n-link>
      </a-menu-item>
      <a-menu-item>
        <n-link to="/actors">
          Actors
        </n-link>
      </a-menu-item>
    </a-menu>
  </a-layout-header>
</template>
<style>
.nav {
  position: 'fixed';
  z-index: 1;
  width: '100%';
}
.menu {
  line-height: '64px';
}
.logo {
  width: 120px;
  height: 31px;
  background: rgba(255, 255, 255, 0.2);
  margin: 16px 24px 16px 0;
  float: left;
}
</style>
