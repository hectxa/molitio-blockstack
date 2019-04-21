<template>
  <div class="hello">
    <div>
      <img src="/logoTop.png" class="landing-logo">
      <h1>Molitio</h1>
      <p>A simple task tracking app</p>
      <button class="sign btn btn-default bg-white" @click.prevent="signIn">Sign In With Blockstack</button>
      <small class="twit">
        <a href="https://twitter.com/hectxa">Twitter</a>
      </small>
      <small class="creds">
        <a href="https://apps.apple.com/eg/app/molitio/id1303186172" target="_blank">Get it on iOS </a>
      </small>
    </div>
  </div>
</template>

<script>
import { userSession } from '../userSession'

export default {
  name: 'landing',
  methods: {
    signIn () {
      userSession.redirectToSignIn()
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello {
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}
.hello h1 {
  font-family: "Segoe UI", sans-serif;
}
.landing-logo {
  width: 40px;
  float: left;
  margin-right: 5px;
}
.sign {
  color: rgba(0, 0, 0, 0.45);
}
.twit {
  position: absolute;
  bottom: 5px;
  left: 5px;
  text-align: left;
}

</style>
