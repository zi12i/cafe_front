<template>
  <div class="login">
    <b-card class="loginContainer">
      <h2><img class="logoImg" src="../../../public/logo.png" /></h2>
      <ValidationObserver ref="signUpForm" v-slot="{ handleSubmit, invalid, validate }">
        <b-form @submit.prevent="handleSubmit(OwnerLogin)">
          <b-form-group class="userContainer">
            <ValidationProvider v-slot="{ errors }" name="userid" rules="required">
              <b-form-input
                v-model="userId"
                :counter="10"
                :error-messages="errors"
                required
                @keyup.enter="OwnerLogin"
              ></b-form-input>
              <label>User Id</label>
            </ValidationProvider>
          </b-form-group>
          <b-form-group class="userContainer">
            <ValidationProvider v-slot="{ errors }" name="password" rules="required|min:6|max:20">
              <b-form-input
                v-model="password"
                :error-messages="errors"
                required
                type="password"
                @keyup.enter="OwnerLogin"
              ></b-form-input>
              <label>Password</label>
            </ValidationProvider>
          </b-form-group>
          <b-form-group>
            <button class="sub" variant="primary" :disabled="invalid || !validate" @click="OwnerLogin">
              <span v-if="loading"></span>
              <span v-if="loading"></span>
              <span v-if="loading"></span>
              <span v-if="loading"></span>
              <!-- <b-spinner v-if="loading" type="submit" small></b-spinner>  -->
              Login
            </button>
          </b-form-group>
          <b-form-group class="ath">
            <button class="athA" variant="primary" @click="signUp">회원가입</button>
            <button class="athB" variant="primary" @click="find">ID/PW찾기</button>
          </b-form-group>
        </b-form>
      </ValidationObserver>
    </b-card>
    <section class="hero">
      <div class="content"></div>
      <div class="waves"></div>
    </section>
  </div>
</template>

<script>
// import jwtDecode from 'jwt-decode'
import Validate from '@/assets/mixins/Validate.vue'
import axios from 'axios'

export default {
  mixins: [Validate],
  data() {
    return {
      userId: '',
      password: '',
      loading: false
    }
  },
  methods: {
    async OwnerLogin() {
      if (this.loading) return
      this.loading = true
      const axiosBody = {
        userId: this.userId,
        password: this.password
      }
      //console.log('아시오스 들어오나', axiosBody)
      await axios
        .post(process.env.VUE_APP_URL + '/auth/login', axiosBody)
        .then(async res => {
          const code = res.data
          localStorage.setItem('token', res.data.token)
          console.log('/auth/login - response: ', code)
          this.$router.push('/main')
        })
        .catch(err => {
          alert('다시 시도해주세요!')
          this.$router.go()
          this.loading = true
        })
    },
    signUp() {
      this.$router.push('/auth/join')
    },
    find() {
      this.$router.push('/auth/find')
    }
  }
}
</script>

<style>
.login {
  width: 100%;
  height: 100vh;
  margin: 0;
  padding: 0;
  font-family: sans-serif;
  background-color: #5a38d4;
}

.loginContainer {
  overflow: hidden;
  position: absolute;
  top: 50%;
  left: 50%;
  width: 400px;
  padding: 40px;
  transform: translate(-50%, -50%);
  background: #fff;
  box-sizing: border-box;
  box-shadow: 0 10px 15px rgba(0, 0, 0, 0.3);
  border-radius: 10px;
  z-index: 1;
}

.loginContainer h2 {
  margin: 0 0 30px;
  padding: 0;
  color: #5a38d4;
  text-align: center;
}

.loginContainer .userContainer {
  position: relative;
}

.loginContainer .userContainer input {
  width: 100%;
  padding: 10px 0;
  font-size: 16px;
  color: #5a38d4;
  margin-bottom: 30px;
  border: none;
  border-bottom: 1px solid #5a38d4;
  outline: none;
  background: transparent;
}
.loginContainer .userContainer label {
  position: absolute;
  top: 0;
  left: 0;
  padding: 10px 0;
  font-size: 16px;
  color: #5a38d4;
  pointer-events: none;
  transition: 0.5s;
}

.loginContainer .userContainer input:focus ~ label,
.loginContainer .userContainer input:valid ~ label {
  top: -20px;
  left: 0;
  color: #5a38d4;
  font-size: 12px;
}

.loginContainer form .sub {
  position: relative;
  border: none;
  /* border: 2px solid #5a38d4; */
  border-radius: 5px;
  background-color: #5a38d4;
  display: inline-block;
  padding: 10px 20px;
  color: #fff;
  font-size: 16px;
  text-decoration: none;
  text-transform: uppercase;
  overflow: hidden;
  transition: 0.5s;
  margin-top: 14px;
  margin-bottom: 22px;
  letter-spacing: 4px;
}

.loginContainer .sub:hover {
  background: #fff;
  color: #5a38d4;
  border-radius: 5px;
  box-shadow: 0 0 5px #5a38d4, 0 0 15px #5a38d4;
}

.loginContainer .ath {
  margin: 10px;
  font-size: 12px;
  text-align: center;
}

.athA {
  border: none;
  background: #fff;
  color: #5a38d4;
  margin-right: 17px;
  transition: 0.5s;
  padding: 8px;
}

.athB {
  border: none;
  background: #fff;
  color: #5a38d4;
  transition: 0.5s;
  padding: 8px;
}

.athA:hover {
  background: #5a38d4;
  color: #fff;
  border-radius: 10px;
}

.athB:hover {
  background: #5a38d4;
  color: #fff;
  border-radius: 10px;
}

.loginContainer form .sub {
  left: 30%;
}

.loginContainer .sub span {
  position: absolute;
  display: block;
}

.loginContainer .sub span:nth-child(1) {
  top: 0;
  left: -100%;
  width: 100%;
  height: 2px;
  background: linear-gradient(90deg, transparent, #5a38d4);
  animation: btn-anim1 1s linear infinite;
}

@keyframes btn-anim1 {
  0% {
    left: -100%;
  }
  50%,
  100% {
    left: 100%;
  }
}

.loginContainer .sub span:nth-child(2) {
  top: -100%;
  right: 0;
  width: 2px;
  height: 100%;
  background: linear-gradient(180deg, transparent, #5a38d4);
  animation: btn-anim2 1s linear infinite;
  animation-delay: 0.25s;
}

@keyframes btn-anim2 {
  0% {
    top: -100%;
  }
  50%,
  100% {
    top: 100%;
  }
}

.loginContainer .sub span:nth-child(3) {
  bottom: 0;
  right: -100%;
  width: 100%;
  height: 2px;
  background: linear-gradient(270deg, transparent, #5a38d4);
  animation: btn-anim3 1s linear infinite;
  animation-delay: 0.5s;
}

@keyframes btn-anim3 {
  0% {
    right: -100%;
  }
  50%,
  100% {
    right: 100%;
  }
}

.loginContainer .sub span:nth-child(4) {
  bottom: -100%;
  left: 0;
  width: 2px;
  height: 100%;
  background: linear-gradient(360deg, transparent, #5a38d4);
  animation: btn-anim4 1s linear infinite;
  animation-delay: 0.75s;
}

@keyframes btn-anim4 {
  0% {
    bottom: -100%;
  }
  50%,
  100% {
    bottom: 100%;
  }
}

.loginContainer .sub:hover span {
  animation: none;
}

:root {
  --color: #5a38d4;
}

.content {
  max-width: 600px;
  margin: 0 auto;
  padding: 0 20px;
}

.hero {
  position: relative;
  background: #333333;
  color: white;
  height: 100vh;
  display: flex;
  align-items: center;
  overflow: hidden;
}

/* ========================= */
.waves {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 200px;
  background-color: var(--color);
  box-shadow: inset 0 0 50px rgba(0, 0, 0, 0.5);
  transition: 500ms;
}

.waves::before,
.waves::after {
  content: '';
  position: absolute;
  width: 300vw;
  height: 300vw;
  top: -70vw;
  left: 50%;
  transform: translate(-50%, -75%);
}

.waves::before {
  border-radius: 44%;
  background: rgb(51, 51, 51);
  animation: waves 8s linear infinite;
}

.waves::after {
  border-radius: 44%;
  background: #f0f2f3;
  animation: waves 15s linear infinite;
}

@keyframes waves {
  0% {
    transform: translate(-50%, -75%) rotate(0deg);
  }
  100% {
    transform: translate(-50%, -75%) rotate(360deg);
  }
}
</style>
