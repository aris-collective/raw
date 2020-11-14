<template>
  <client-only>
    <div class="h-screen">
      <header class="flex-1 overflow-y-auto bg-gray-100">
        <div class="container justify-around items-center">
          <div>
            <div class="logo"><a href="#" class="logo">RAWZIK</a></div>
          </div>
          <div class="mr-auto space-x-auto">
            <button class="hover:bg-gray-400 h-20 px-2">
              <p class="font-semibold">RELEASES</p>
            </button>
            <button class="hover:bg-gray-400 h-20 px-2">
              <p class="font-semibold">SCENE</p>
            </button>
            <button class="hover:bg-gray-400 h-20 px-2">
              <p class="font-semibold">CHARTS</p>
            </button>
            <button class="hover:bg-gray-400 h-20 px-2">
              <p class="font-semibold">PROMOS</p>
            </button>
            <button class="hover:bg-gray-400 h-20 px-2">
              <p class="font-semibold">PACKS</p>
            </button>
          </div>
        </div>
      </header>
      <Nuxt /></div
  ></client-only>
</template>

<style>
html {
  font-family: 'segoeuib', 'Helvetica Neue', Helvetica, Arial, sans-serif;
  font-size: 14px;
  line-height: 15px;
  color: #171718!important;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
}

*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
}

.button--green {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #3b8070;
  color: #3b8070;
  text-decoration: none;
  padding: 10px 30px;
}

.button--green:hover {
  color: #fff;
  background-color: #3b8070;
}

.button--grey {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #c4c7ca;
  color: #35495e;
  text-decoration: none;
  padding: 10px 30px;
  margin-left: 15px;
}

.button--grey:hover {
  color: #fff;
  background-color: #35495e;
}

body {
  background-color: #35495e;
}

.logo {
  font-family: 'Staatliches', 'Helvetica Neue', Helvetica, Arial, sans-serif;
  margin-left: 3px;
  color:#35495e;
  font-size: 34px;
  width: 120px;
}

.sidebar-button {
  border-left: #282c31 7px solid;
}
</style>
