<template>
  <div class="container">
    <div class="row">
      <div class="col-md-6 col-md-offset-3">
        <div class="panel panel-login">
          <div class="panel-body">
            <div class="row">
              <div class="col-lg-12">
                <form id="login-form" action="#" method="post" role="form" style="display: block;">
                  <h2>REGISTER</h2>
                  <div class="form-group">
                    <input type="text" class="form-control" placeholder="Firstname" v-model="firstname">
                  </div>
                  <div class="form-group">
                    <input type="text" class="form-control" placeholder="Lastname" v-model="lastname">
                  </div>
                  <div class="form-group">
                    <input type="text" class="form-control" placeholder="Department" v-model="department">
                  </div>
                  <div class="form-group">
                    <input type="email" class="form-control" placeholder="Email Address" v-model="email">
                  </div>
                  <div class="form-group">
                    <input type="email" class="form-control" placeholder="Phone Number" v-model="phoneNumber">
                  </div>
                  <div class="form-group">
                    <input type="password" class="form-control" placeholder="Password" v-model="password">
                  </div>
                  <div class="form-group">
                    <input type="password" class="form-control" placeholder="Confirm Password" v-model="confirmpassword">
                  </div>
                  <br><br>
                  <div class="form-group">
                    <div class="row">
                      <div class="col-sm-6 col-sm-offset-3">
                        <button type="button" @click="submitRegister()" class="form-control btn btn-register">Register Now</button>
                      </div>
                    </div>
                  </div>
                </form>
              </div>
            </div>
          </div>
          <br><br>
          <div class="panel-heading">
            <div class="row">
              <div class="col-xs-6 tabs">
                <router-link to="/"><a href="#" class="active" id="login-form-link"><div class="login">LOGIN</div></a></router-link>
              </div>
              <div class="col-xs-6 tabs">
                <router-link to="/signupform"><a href="#" id="register-form-link"><div class="register action">REGISTER</div></a></router-link>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {userRef, auth} from './firebase'

export default {
  name: 'signupform',
  data () {
    return {
      firstname: '',
      lastname: '',
      email: '',
      password: '',
      confirmpassword: '',
      department: '',
      phoneNumber: ''
    }
  },
  created () {
  },
  firebase: {
    users: userRef
  },
  methods: {
    submitRegister () {
      var re = /[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}/igm
      var phoneNumber = /^\d{10}$/
      var passCheck = this.password.length
      if (this.password !== this.confirmpassword) {
        alert('กรุณากรอกรหัสผ่านทั้ง 2 ช่องให้ตรงกัน')
      } else if (this.firstname === '' || this.lastname === '' || this.email === '' || this.password === '' || this.confirmpassword === '' || this.department === '' || this.phoneNumber === '') {
        alert('กรุณากรอกข้อมูลให้ครบถ้วน')
      } else if (re.test(this.email) === false) {
        alert('รูปแบบ email ไม่ถูกต้อง')
      } else if (phoneNumber.test(this.phoneNumber) === false) {
        alert('รูปแบบเบอร์โทรไม่ถูกต้อง')
      } else if (passCheck < 6) {
        alert('ใส่รหัสผ่านอย่างน้อย 6 ตัวขึ้นไป')
      } else {
        userRef.push({
          firstname: this.firstname,
          lastname: this.lastname,
          email: this.email.toLowerCase(),
          password: this.password,
          confirmpassword: this.confirmpassword,
          department: this.department,
          phoneNumber: this.phoneNumber,
          status: 'user',
          editProfile: false,
          agree: 'ยังไม่รับทราบ',
          noteNoti: 0
        })
        auth.createUserWithEmailAndPassword(this.email, this.password).catch((error) => {
          var errorCode = error.code
          var errorMessage = error.message
          if (errorCode === 'auth/wrong-password') {
            alert('pass 6')
          } else {
            alert(errorMessage)
          }
          console.log(error)
        })
        alert('ลงทะเบียนสำเร็จ')
        this.$router.push('/')
      }
    }
  }
}
</script>

<style scoped>
@import url(https://fonts.googleapis.com/css?family=Roboto:400,300,100,700,500);
.action{
  background: #2d3b55;
  color: #ffffff;
}
.panel {
  width: 450px;
  margin: auto;
}
.container {
  padding-top: 70px;
  background:#F7F7F7;
  color:#666666;
  font-family: 'Roboto', sans-serif;
  font-weight:100;
  /* position: fixed; */
  z-index: 1000;
  top: 0;
  bottom: 0;
}

.container {
  width: 100%;
  /* background: -webkit-linear-gradient(left, #22d686, #24d3d3, #22d686, #24d3d3);
  background: linear-gradient(to right, #22d686, #24d3d3, #22d686, #24d3d3);
  background-size: 600% 100%;
  -webkit-animation: HeroBG 20s ease infinite;
          animation: HeroBG 20s ease infinite; */
}

@-webkit-keyframes HeroBG {
  0% {
    background-position: 0 0;
  }
  50% {
    background-position: 100% 0;
  }
  100% {
    background-position: 0 0;
  }
}

@keyframes HeroBG {
  0% {
    background-position: 0 0;
  }
  50% {
    background-position: 100% 0;
  }
  100% {
    background-position: 0 0;
  }
}


.panel {
  border-radius: 5px;
}
label {
  font-weight: 300;
}
.panel-login {
   border: none;
  -webkit-box-shadow: 0px 0px 49px 14px rgba(188,190,194,0.39);
  -moz-box-shadow: 0px 0px 49px 14px rgba(188,190,194,0.39);
  box-shadow: 0px 0px 49px 14px rgba(188,190,194,0.39);
  }
.panel-login .checkbox input[type=checkbox]{
  margin-left: 0px;
}
.panel-login .checkbox label {
  padding-left: 25px;
  font-weight: 300;
  display: inline-block;
  position: relative;
}
.panel-login .checkbox {
 padding-left: 20px;
}
.panel-login .checkbox label::before {
  content: "";
  display: inline-block;
  position: absolute;
  width: 17px;
  height: 17px;
  left: 0;
  margin-left: 0px;
  border: 1px solid #cccccc;
  border-radius: 3px;
  background-color: #fff;
  -webkit-transition: border 0.15s ease-in-out, color 0.15s ease-in-out;
  -o-transition: border 0.15s ease-in-out, color 0.15s ease-in-out;
  transition: border 0.15s ease-in-out, color 0.15s ease-in-out;
}
.panel-login .checkbox label::after {
  display: inline-block;
  position: absolute;
  width: 16px;
  height: 16px;
  left: 0;
  top: 0;
  margin-left: 0px;
  padding-left: 3px;
  padding-top: 1px;
  font-size: 11px;
  color: #555555;
}
.panel-login .checkbox input[type="checkbox"] {
  opacity: 0;
}
.panel-login .checkbox input[type="checkbox"]:focus + label::before {
  outline: thin dotted;
  outline: 5px auto -webkit-focus-ring-color;
  outline-offset: -2px;
}
.panel-login .checkbox input[type="checkbox"]:checked + label::after {
  font-family: 'FontAwesome';
  content: "\f00c";
}
.panel-login>.panel-heading .tabs{
  padding: 0;
}
.panel-login h2{
  font-size: 20px;
  font-weight: 300;
  margin: 30px;
}
.panel-login>.panel-heading {
  color: #848c9d;
  background-color: #e8e9ec;
  border-color: #fff;
  text-align:center;
  border-bottom-left-radius: 5px;
  border-bottom-right-radius: 5px;
  border-top-left-radius: 0px;
  border-top-right-radius: 0px;
  border-bottom: 0px;
  padding: 0px 15px;
}
.panel-login .form-group {
  padding: 0 30px;
}
.panel-login>.panel-heading .login {
  padding: 20px 30px;
  border-bottom-left-radius: 5px;
}
.panel-login>.panel-heading .login:hover{
  padding: 20px 30px;
  background: #2d3b55;
  border-bottom-left-radius: 5px;
  color: #ffffff;
}
.panel-login>.panel-heading .register {
  padding: 20px 30px;
  background: #2d3b55;
  border-bottom-right-radius: 5px;
}
.panel-login>.panel-heading a{
  text-decoration: none;
  color: #666;
  font-weight: 300;
  font-size: 16px;
  -webkit-transition: all 0.1s linear;
  -moz-transition: all 0.1s linear;
  transition: all 0.1s linear;
}
.panel-login>.panel-heading a#register-form-link {
  color: #fff;
  width: 100%;
  text-align: right;
}
.panel-login>.panel-heading a#login-form-link {
  width: 100%;
  text-align: left;
}

.panel-login input[type="text"],.panel-login input[type="email"],.panel-login input[type="password"] {
  height: 45px;
  border: 0;
  font-size: 16px;
  -webkit-transition: all 0.1s linear;
  -moz-transition: all 0.1s linear;
  transition: all 0.1s linear;
  -webkit-box-shadow: none;
  box-shadow: none;
  border-bottom: 1px solid #e7e7e7;
  border-radius: 0px;
  padding: 6px 0px;
}
.panel-login input:hover,
.panel-login input:focus {
  outline:none;
  -webkit-box-shadow: none;
  -moz-box-shadow: none;
  box-shadow: none;
  border-color: #ccc;
}
.btn-login {
  background-color: #E8E9EC;
  outline: none;
  color: #2D3B55;
  font-size: 14px;
  height: auto;
  font-weight: normal;
  padding: 14px 0;
  text-transform: uppercase;
  border: none;
  border-radius: 0px;
  box-shadow: none;
}
.btn-login:hover,
.btn-login:focus {
  color: #fff;
  background-color: #2D3B55;
}
.forgot-password {
  text-decoration: underline;
  color: #888;
}
.forgot-password:hover,
.forgot-password:focus {
  text-decoration: underline;
  color: #666;
}

.btn-register {
  background-color: #57b846;
  outline: none;
  color: #ffffff;
  font-size: 14px;
  height: auto;
  font-weight: normal;
  padding: 14px 0;
  text-transform: uppercase;
  border: none;
  border-radius: 0px;
  box-shadow: none;
  box-shadow: 0 1px 8px 0 rgba(0, 0, 0, 0.5), 0 1px 20px 0 rgba(0, 0, 0, 0.19);
}
.btn-register:hover,
.btn-register:focus {
  color: #fff;
  background-color: #2D3B55;
}
</style>
