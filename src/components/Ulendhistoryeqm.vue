<template>
  <div>
    <div class="nav-header">
      <ul>
        <li class="topic">
          <p><router-link to="/ulendhistory" style="font-size:25px;color:#ffffff;">ประวัติการยืม</router-link> /</p>
        </li>
        <li class="topic" style="padding-left: 10px;">
          <p style="font-size:25px; border-bottom: 2px solid #ffffff"><b>{{nameEqm}}</b></p>
        <li style="font-size:15px;color:#2c3e50;float:right;">
        <li style="font-size:15px;color:#2c3e50;float:right;">
          <div class="dropdown" style="float:right;">
            <span class="dropbtn glyphicon glyphicon-chevron-down" style="color:#ffffff;"></span>
            <div class="dropdown-content">
              <a href="#" @click="submitLogout()"><span class="glyphicon glyphicon-log-out"></span> ออกจากระบบ</a>
            </div>
          </div>
        </li>
        <li class="user-login">
          <p style="font-size:28px;margin-top:-15px;color:#ffffff;">{{firstname}} {{lastname}}</p>
          <p style="font-size:20px;margin-top:-45px;font-style:italic;color:#ffffff;">General user</p>
        </li>
      </ul>
    </div>

    <nav>
      <img src="../assets/196127.png" style="margin-top:22px;margin-left:20px;width:40%;">
      <p class="navbar-brand">ระบบจัดการ<br>อุปกรณ์ทางการแพทย์</p>
      <br><br><br><br><br>
      <ul>
        <!-- <li>
          <i class="glyphicon glyphicon-wrench" style="font-size:25px;"></i>
          <router-link to="/uhome">ยืมเครื่องมือแพทย์</router-link>
        </li> -->
        <li>
          <i class="glyphicon glyphicon-wrench" style="font-size:25px;color:#ffffff;"></i>
          <router-link to="/ubookeqm">ยืมเครื่องมือแพทย์</router-link>
        </li>
        <li>
          <i class="fa fa-check-square-o" style="color:#ffffff;font-size:25px;"></i>
          <router-link to="/uapprove">รายการรออนุมัติ</router-link>
        </li>
        <li>
          <i class="glyphicon glyphicon-book" style="color:#ffffff;font-size:25px;"></i>
          <button v-if="user.noteNoti !== 0 && user.email === emailAuth" v-for="user of users" class="noti" style="margin-left:-12px;">
            <p style="margin-top: -4px;">{{user.noteNoti}}</p>
          </button>
          <router-link to="/ulisttable">รายการเครื่องมือที่ยืมมา</router-link>
        </li>
        <li>
          <i class="glyphicon glyphicon-send" style="color:#ffffff;font-size:25px;"></i>
          <router-link to="/urequest">แจ้งความต้องการใช้อุปกรณ์</router-link>
        </li>
        <li class="selected">
          <i class="fa fa-clipboard" style="font-size:25px;"></i>
          <router-link to="/ulendhistory">ประวัติการยืม</router-link>
        </li>
        <li class="active-loguot">
          <i class="glyphicon glyphicon-off" style="color:red;font-size:25px;"></i>
          <a href="#" @click="submitLogout()">ออกจากระบบ</a>
        </li>
      </ul>
    </nav>

    <div class="content">
      <div class="container-fluid">
        <div class="row">
          <div class="col-md-9">
          <div class="card">
            <div class="card-block">
              <h4 class="card-title" style="font-size:20px">
                {{nameEqm}}
              </h4>

              <br>
              <table class="table">
                <thead>
                  <tr>
                    <th width="150px">วันที่คืน</th>
                    <th width="500px">หมายเลขเครื่อง</th>
                    <th width="100px">สถานะ</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="arrayEqms in arrayEqm" v-if="arrayEqms.number !== ''">
                    <td>{{arrayEqms.date}}</td>
                    <td >{{arrayEqms.number}}</td>
                    <td>{{arrayEqms.status}}</td>
                  </tr>
                </tbody>
              </table>

            </div>
          </div>
          </div>
          <!--
          <div class="col-sm-3">
          <div class="card">
            <div class="card-block">
              <h4 class="card-title">
                แสดงรายการเครื่องมือที่เปิดให้ยืมss
              </h4>
            </div>
          </div>
          </div>!-->
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import {equipmentRef, auth, userRef, historyRef, messaging} from './firebase'

export default {
  name: 'ulendhistoryeqm',
  data () {
    return {
      name: '',
      category: '',
      amountEqm: '',
      count: '',
      nameEqm: '',
      unitEqm: '',
      categoryEqm: '',
      firstname: '',
      lastname: '',
      editName: '',
      editCategory: '',
      editID: '',
      key: '',
      arrayEqm: [],
      msgLogout: 'ออกจากระบบ',
      keyPushNoti: '',
      emailAuth: ''
    }
  },
  created () {
    auth.onAuthStateChanged((user) => {
      if (user) {
        this.firstname = this.users.find(users => users.email === user.email).firstname
        this.lastname = this.users.find(users => users.email === user.email).lastname
        this.keyPushNoti = this.users.find(users => users.email === user.email)['.key']
        this.emailAuth = user.email
      } else {
        console.log('not logged in')
      }
    })
    this.arrayEqm = this.historys.find(historys => historys['.key'] === this.$route.params.id).returnedDate
    this.nameEqm = this.historys.find(historys => historys['.key'] === this.$route.params.id).nameEqm
  },
  firebase: {
    equipments: equipmentRef,
    users: userRef,
    historys: historyRef
  },
  methods: {
    removeEqm (key) {
      equipmentRef.child(key).remove()
    },
    submitLogout () {
      messaging.getToken().then((token) => messaging.deleteToken(token))
      userRef.child(this.keyPushNoti).update({
        keyPushNoti: ''
      })
      auth.signOut()
      this.$router.push('/')
    }
  }
}
</script>

<style scoped>
/*--------------------------------------- CONTENT ----------------------------------*/
.row {
  width: 80%;
}
.card {
  padding: .75rem 1.25rem;
  margin-bottom: 0;
  background-color: #ffffff;
  border-bottom: 1px solid rgba(0,0,0,.125);
  border-radius: 4px;
  margin-right: 24px;
  margin-left: 48px;
  box-shadow: 0 1px 8px 0 rgba(0, 0, 0, 0.1), 0 1px 20px 0 rgba(0, 0, 0, 0.19);
}

.button-add {
  float: right;
  margin-left: 10px;
}

.content {
  margin-top: 60px;
  margin-left: 275px;
  padding: 20px 0px;
}
/*----------------------------------------------------------------------------------*/

/*------------------------------------- HEADER -------------------------------------*/
.nav-header {
  height: 60px;
  width: 100%;
  background: rgb(3,155,229);
  padding-left: 20px;
  display: inline-block;
  line-height: 60px;
  box-shadow: 0 1px 8px 0 rgba(0, 0, 0, 0.5), 0 1px 20px 0 rgba(0, 0, 0, 0.19);
  bottom: 0;
  position: fixed;
  top: 0;
  margin-top: -1px;
}

.nav-header ul li p {
  font-weight: 400;
  font-size: 20px;
  height: 58px;
}

.nav-header ul li {
  display: inline-block;
}
.nav-header ul .user-login {
  float: right;
  padding-right: 10px;
  color: #9A9A9A;
}

.nav-header ul .topic {
  padding-left: 301px;
  float: left;
  color: #9A9A9A;
}

.nav-header ul .user-login p {
  font-size: 16px;
}

.nav-header ul .topic p {
  font-size: 20px;
  color: #ffffff;
}

.navbar-brand {
  color: #FFFFFF;
  line-height: 30px;
  padding-left: 147px;
  /*border-bottom: 1px solid #525d63;*/
  width: 100%;
  height: auto;
  font-weight: bold;
  font-size: 20px;
  margin-top: -108px;
}
/*----------------------------------------------------------------------------------*/

/*--------------------------------------- MENU -------------------------------------*/
nav {
  width: 301px;
  background: #262f3d;
  position: fixed;
  z-index: 1000;
  top: 0;
  bottom: 0;
  box-shadow: 0 1px 8px 0 rgba(0, 0, 0, 0.5), 0 1px 20px 0 rgba(0, 0, 0, 0.19);
}

nav a {
  text-decoration: none;
  color:  #ffffff;
  padding: 20px;
  padding-right: 100px;
}

nav ul li {
  list-style-type: none;
  display: block;
 
  padding: 15px;
  padding-left: 30px;
 
  font-size: 20px;
}
 
nav ul {
  margin-top: -50px;
}

.active-loguot {
  position: absolute;
  margin-left: 6px;
  width: 289px;
  bottom: 20px;
  background: rgba(255, 255, 255, 0.14);
  opacity: 1;
  color: #FFFFFF;
  border-radius: 4px;
}

nav ul li:hover {
  background: #373f4c;
  transition: linear all 0.30s;
}

nav ul li a:hover {
  transition: linear all 0.50s;
}

.selected a, i {
  color: #4fc3f7;
}
/*----------------------------------------------------------------------------------*/

/*--------------------------------------- selectBox -------------------------------------*/
.selectBox {
  width: 150px;
  height: 34px;
  border-radius: 4px;
  font-size: 14px;
}
/*----------------------------------------------------------------------------------*/
td {
  font-size: 20px;
}
th {
  font-size: 20px;
}

.dropbtn {
    color:#337ab7;
    padding: 16px;
    font-size: 16px;
    border: none;
    cursor: pointer;
}

.dropdown {
    position: relative;
    display: inline-block;
}

.dropdown-content {
    display: none;
    position: absolute;
    right: 0;
    background-color: #ffffff;
    min-width: 160px;
    border: 1px solid #dddddd;
    box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
    z-index: 1;
}

.dropdown-content a {
    color: #337ab7;
    padding: 1px 18px;
    text-decoration: none;
    height: 50px;
    display: block;
}

.dropdown-content a:hover {background-color: #f1f1f1}

.dropdown:hover .dropdown-content {
    display: block;
}

.noti {
  height:20px;
  width:20px;
  border-radius:60px;
  border:1px solid #d9534f;
  background:#d9534f;
  color:#ffffff;
  font-size:16px;
  position:absolute;
  margin-left:-10px;
  margin-top:-5px;
}
</style>
