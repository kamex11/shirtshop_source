<template>
  <div class="home">
    <div class="welcome">
      <div class="welcome__text">
        Witaj w koszulkowie
      </div>
      <button class="welcome__button">
        <router-link class="link" to="/shop">Stwórz koszulkę</router-link>
      </button>
    </div>
  </div>
</template>

<script>

export default {
  name: 'Home',
};
</script>
<style>
.home {
  flex-grow: 1;
}
.welcome {
  width: 100%;
  height: 80vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  align-content: center;
  gap: 20px;
}
.welcome__text{
  font-family: 'Manrope', sans-serif;
  font-size: 60px;
  color: white;
  font-weight: 700;
  text-decoration: none;
}
.welcome__button{
  background-image: linear-gradient(-180deg, #FF9500 0%, #FF5E3A 100%);
  border-radius: 4px;
  box-sizing: border-box;
  color: #FFFFFF;
  display: flex;
  justify-content: center;
  padding: 1rem 2rem;
  text-decoration: none;
  border: 0;
  cursor: pointer;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  font-family: 'Manrope', sans-serif;
  font-size: 30px;
}
.welcome__button:hover {
  background-image: linear-gradient(180deg, #a744a2 0%, #ff5c00 100%);
}
.link {
  text-decoration: none;
  color: white!important;
}

</style>
