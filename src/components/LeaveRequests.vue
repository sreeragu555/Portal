<template>
    <div>

        <md-table >
                
            <md-table-row>
                <md-table-head >ID</md-table-head>
                <md-table-head>From date</md-table-head>
                <md-table-head>To Date</md-table-head>
                <md-table-head>Type of leave</md-table-head>
                <md-table-head>Status</md-table-head>
                <md-table-head>Remarks</md-table-head>
            </md-table-row>
        
            <md-table-row v-for="item in leaves" :key="item.collection_id">
                <md-table-cell >{{item.collection_id}}</md-table-cell>
                <md-table-cell >{{item.From_Date.getDate()}}/{{item.From_Date.getMonth()+1}}/{{item.From_Date.getFullYear()}}</md-table-cell>
                <md-table-cell >{{item.To_Date.getDate()}}/{{item.To_Date.getMonth()+1}}/{{item.To_Date.getFullYear()}}</md-table-cell>
                <md-table-cell >{{item.Type_of_leave}}</md-table-cell>
                <md-table-cell >{{item.Status}}</md-table-cell>
                <md-table-cell >{{item.Remarks}}</md-table-cell>
            </md-table-row>
        </md-table>
 <!-- 
        <md-table v-model="leaves" >
      <md-table-toolbar>
        <h1 class="md-title">Users</h1>
      </md-table-toolbar>
      <md-table-row slot="md-table-row" slot-scope="{ item }">
        <md-table-cell md-label="ID">{{ item.collection_id }}</md-table-cell>
        <md-table-cell md-label="From Date">{{item.From_Date.getDate()}}/{{item.From_Date.getMonth()+1}}/{{item.From_Date.getFullYear()}}</md-table-cell>
        <md-table-cell md-label="To Date">{{item.To_Date.getDate()}}/{{item.To_Date.getMonth()+1}}/{{item.To_Date.getFullYear()}}</md-table-cell>
        <md-table-cell md-label="Type of leave">{{ item.Type_of_leave }}</md-table-cell>
        <md-table-cell md-label="Status">{{ item.Status }}</md-table-cell>
        <md-table-cell md-label="Remarks">{{ item.Remarks }}</md-table-cell>
      </md-table-row>
    </md-table>
    -->

    </div>
</template>
<script>
import db from '@/firebase/init'

import firebase from 'firebase'

export default {
name:'leaveRequests',
    data(){
        return{            
            leaves:[]
        }
        
        loadedData:false
    },
methods:
{

 
},
created(){

},

    beforeCreate(){
                firebase.auth().onAuthStateChanged(authUser=> {
                    if (authUser!= null) {
                                //console.log(authUser.uid)
                                
                               let dbref=db.collection("Leave_requests").where("Employees_id","==",authUser.uid)
                                 dbref.get().then(snapshot=>{
                                     let i=0
                                    snapshot.forEach(emp => {
                                     //   console.log(emp)
                                 this.leaves[i]=emp.data()
                                 this.leaves[i].collection_id=emp.id
                                 this.leaves[i].To_Date=this.leaves[i].To_Date.toDate()
                                 this.leaves[i].From_Date=this.leaves[i].From_Date.toDate()
                                 this.loadedData=true
                                 console.log(leaves)
                                 i++
                         });
                     }).catch(err=>{
                        console.log(err.message)
                     })

                     } else {
                                this.user=null,
                                console.log("No users logged in")
                                this.$router.push("/Login")
                            }
                    });
                
    }
}
</script>
<style>

</style>
