<template>
  <div id="app">
    <div class="navigation" id="nav">
      <router-link class="navlink" to="/">
        <div class="navigation__logo">
          <img alt="Vue logo" src="@/assets/logo.png">
          <div class="mobileHidden">koszulkowo</div>
        </div>
      </router-link>
      <router-link class="navigation__link" to="/">Home</router-link>
      <router-link class="navigation__link" to="/shop">Sklep</router-link>
    </div>
    <router-view/>
    <div class="footer">
      Made with passion by Kamil Łącki
    </div>
  </div>
</template>
<style>

body {
  margin: 0;
  font-family: 'Roboto', sans-serif;
}

.navlink {
  color:black!important;
}
.footer {
  width: 100%;
  height: 60px;
  background-color: white;
  bottom: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  color: gray;
  font-family: 'Roboto', sans-serif;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  background: linear-gradient(-45deg, #ee7752, #e73c7e, #23a6d5, #23d5ab);
  background-size: 400% 400%;
  animation: gradient 25s ease infinite;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
}

#nav {
  height: 60px;
  background-color: white;
  display: flex;
  vertical-align: center;
  align-items: center;
  flex-direction: row;
  margin-bottom: 20px;
  box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.24);
}

#nav a {
  font-weight: bold;
  color: black;
  text-decoration: none;
}

#nav a.router-link-exact-active {
  color: darkorange;
}

.navigation__logo {
  max-height: 50px;
  margin-left: 10px;
  margin-right: 50px;
  font-family: 'Manrope', sans-serif;
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 20px;
  font-size: 32px;
  font-weight: 800;
  cursor: pointer;
}

.navigation__link {
  padding-left:10px;
  padding-right: 10px;
}

@media screen and (max-width: 600px) {
  .mobileHidden {
    display:none;
  }
}

@keyframes gradient {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}
</style>
