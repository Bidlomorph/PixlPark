<template>
  <div id="app">
    <div class="order" v-for="(order, itemId) in orders" :key="itemId">
      {{ order }}
    </div>
  </div>
</template>

<script>
import axios from "axios"
let sha1 = require('sha1');
export default {
  data () {
    return {
      accessToken: null,
      requestToken: null,
      privateKey: '8e49ff607b1f46e1a5e8f6ad5d312a80',
      publicKey: '38cd79b5f2b2486d86f562e3c43034f8',
      orders: [],
    }
  },
  name: 'App',
  methods: {
      async getRequestToken() {
          const url = '/oauth/requesttoken'
          let requestParams = {
              url: url,
              method: "GET",
          }
          await axios(requestParams).then(
              resp => {
                  this.requestToken = resp.data.RequestToken
              },
              err => {
                  console.log(err)
              }
          )
      },
      async getAccessToken() {
          const url = '/oauth/accesstoken'
          let password = sha1(this.requestToken + this.privateKey)
          let requestParams = {
              url: url,
              method: "GET",
              params: {
                  oauth_token: this.requestToken,
                  grant_type: 'api',
                  username: this.publicKey,
                  password: password,
              }
          }
          await axios(requestParams).then(
              resp => {
                  this.accessToken = resp.data.AccessToken
              },
              err => {
                  console.log(err)
              }
          )
      },
      async getOrders() {
          const url = '/orders'
          let requestParams = {
              url: url,
              method: "GET",
              params: {
                  oauth_token: this.accessToken,
              }
          }
          await axios(requestParams).then(
              resp => {
                  this.orders = resp.data.Result
                  console.log(this.orders)
              },
              err => {
                  console.log(err)
              }
          )
      }
  },
  async mounted() {
      await this.getRequestToken()
      await this.getAccessToken()
      await this.getOrders()
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}
  .order {
    border: 1px solid #2c3e50;
    padding: 5px;
  }
</style>
