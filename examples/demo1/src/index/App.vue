<template>
  <div>
    <ul class="tabbar">
      <li><router-link class="link" to="/test/aaa">aaa</router-link></li>
      <li><router-link class="link" to="/test/bbb">bbb</router-link></li>
    </ul>
    <router-view></router-view>
    <button class="btn" @click="throwError">throw an error</button>
    <Footer/>
  </div>
</template>

<script>
import Footer from '../common/Footer.vue'

export default {
  name: 'App',
  components: {
    Footer,
  },
  methods: {
    throwError() {
      setTimeout(() => {
        throw new Error('I am an error')
      }, 0)
    },
  },
}
</script>

<style>
.tabbar {
  margin-top: 20px;
  padding: 0;
  width: 100%;
  display: flex;
  list-style: none;
  justify-content: center;
}
.tabbar li {
  position: relative;
  display: block;
  height: 50px;
  width: 80px;
  text-align: center;
  line-height: 50px;
  background: #dff1e7;
  margin: 5px;
}
.tabbar li .link {
  display: block;
  width: 100%;
  height: 100%;
}
.btn {
  display: block;
  margin: 15px auto;
  width: 250px;
  height: 40px;
  line-height: 40px;
  text-align: center;
  color: #000;
  font-size: 16px;
  background: #dff1e7;
  border-radius: 5px;
}
</style>
