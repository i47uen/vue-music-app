<template>
  <header>
    <h1>
      <img src="../assets/images/logo.png" alt="Vue">
      My Music App</h1>
  </header>
</template>

<style scoped lang="scss">
@import "../assets/scss/variables";
header{
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  padding: 15px;
  background-color: $dark;
  color: $white;
}
h1{
  display: flex;
  align-items: center;
  justify-content: center;
  color: #41b883;
  font-weight: 400;
}
img{
  margin-right: 15px;
  width: 40px;
}
</style>