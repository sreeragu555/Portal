<template>
    <div name="Profile">
            <div name="sideNav" >
                <div class="tab">
                    <button class="tablinks" @click="openTab('profile')" v-bind:class="{'active':homeTab}">Profile</button>
                    <button class="tablinks" @click="openTab('apply_For_Leave')" v-bind:class="{'active':apply}" >Apply for Leave</button>
                    <button class="tablinks" @click="openTab('leave_Requests')" v-bind:class="{'active':request}">Leave Requests</button>
                </div>
            </div>
                <div id="profile" v-if="homeTab" class="tabcontent">
                    <h3 class="center">Hi {{employee.Name}}</h3>
                    <p class="center">Welcome</p>
                </div>
                <div id="apply_For_Leave" v-if="apply" class="tabcontent">
                    <h3>Apply leave</h3>
                    <p>will update soon</p>
                </div>
                <div id="leave_Requests" v-if="request" class="tabcontent">
                    <h3>List of applied leaves</h3>
                    <p>will update soon</p>
                </div>
        </div>
</template>
<script>
import db from '@/Firebase/init'
import firebase from 'firebase'

export default {
    name:"Profile",
    data(){
        return{
            employee:[],
            homeTab:true,
            apply:null,
            request:null
        }
    },
    methods:{
        openTab(tabID){
            if(tabID=="profile")
            {
                this.homeTab=true,
                this.apply=null,
                this.request=null
            }
            else if(tabID=="apply_For_Leave"){
                this.homeTab=null,
                this.apply=true,
                this.request=null
            }
            else{
                this.homeTab=null,
                this.apply=null,
                this.request=true
            }
        }
    },
    created(){
                firebase.auth().onAuthStateChanged(authUser=> {
                    if (authUser!= null) {
                                //console.log(authUser.uid)
                               let dbref=db.collection("Employees").where("Employees_id","==",authUser.uid)
                                 dbref.get().then(snapshot=>{
                                    snapshot.forEach(emp => {
                                 this.employee=emp.data()
                         });
                     }).catch(err=>{
                        console.log(err.message)
                     })

                     } else {
                                this.user=null,
                                console.log("No users logged in")
                            }
                    });
                
    }
}
</script>
<style>
.tab {
  float: left;
  border: 1px solid #ccc;
  background-color: #f1f1f1;
  width: 30%;
  height: 520px;
}

/* Style the buttons that are used to open the tab content */
.tab button {
  display: block;
  background-color: inherit;
  color: black;
  padding: 22px 16px;
  width: 100%;
  border: none;
  outline: none;
  text-align: left;
  cursor: pointer;
  transition: 0.3s;
}

/* Change background color of buttons on hover */
.tab button:hover {
  background-color: #ddd;
}

/* Create an active/current "tab button" class */
.active {
  background-color: #ccc;
}

/* Style the tab content */
.tabcontent {
  float: left;
  padding: 0px 12px;
  border: 1px solid #ccc;
  width: 70%;
  border-left: none;
  height: 520px;
}
</style>