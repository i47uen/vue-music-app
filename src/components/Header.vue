<template>
  <header>
    <h1>My <img src="../assets/images/logo.png" alt="Vue" /> Music App</h1>
  </header>
</template>

<style scoped lang="scss">
@import "../assets/scss/variables";
header {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  padding: 10px;
  background-color: $dark;
  color: $white;
  user-select: none;
}
h1 {
  display: flex;
  align-items: center;
  justify-content: center;
  color: #41b883;
  font-weight: 400;
}
img {
  margin: 0 8px;
  width: 40px;
}
</style>