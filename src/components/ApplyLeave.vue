<template>
<div>
    <div v-if="loadedData">
        <form novalidate class="md-layout md-alignment-center-center " md-elevation="4" @submit.prevent="can_We_Apply">
                <md-card class="md-layout-item md-size-80 md-small-size-200 mycenter">
                    <md-card-header class="md-layout md-alignment-center-center">
                    <div class="md-title ">APPLY FOR LEAVE</div>
                </md-card-header>
                    <md-card-content>
                      <div>CL:{{leaves.Casual_Leave}}&nbsp;&nbsp;&nbsp;&nbsp;SL:{{leaves.Sick_Leave}}&nbsp;&nbsp;&nbsp;&nbsp;EL:{{leaves.Earned_Leave}}</div>
                    
                    <div class="md-layout-item md-size-50">
                        <!--From date picker-->
        
                        <md-datepicker name="from" v-model="form.selectedFromDate" :md-disabled-dates="disabledDates">
                        <label for="from">From</label>
                        </md-datepicker>
                        </div>
                    
                    <div class="md-layout-item md-size-50">
                        
                      <!--To date picker-->
                        
                        
        
                        <md-datepicker name="to" v-model="form.selectedToDate" :md-disabled-dates="disabledDates">
                        <label for="to">To</label>
                        </md-datepicker>
                    </div>  
                    <div class="md-layout-item md-size-50">
                        <md-field>
                            <label for="type_of_leave">Type of leave</label>

                            <md-select v-model="form.type_of_leave" name="type_of_leave" id="type_of_leave" >
                                <md-option value="Casual">Casual leave</md-option>
                                <md-option value="Sick">Sick leave</md-option>
                                <md-option value="Earned">Earned leave</md-option>
                                <md-option value="Leave-without-pay">Leave without pay</md-option>
                            </md-select>
                        </md-field>
                    </div>
                    <div class="md-layout-item md-size-50">
                            <md-field>
                            <label>Remarks</label>
                            <md-textarea v-model="form.remark"></md-textarea>
                            </md-field>

                    </div>
                  
                    </md-card-content>
                    <md-card-actions>
                    <md-button type="submit" class="md-primary" :disabled="sending">Apply leave</md-button>
                    </md-card-actions>
                </md-card>
                 <md-snackbar :md-active.sync="dispalert">{{message}}</md-snackbar>
        </form>
    </div>
    <div v-if="!loadedData" class="md-layout md-alignment-center-center mycenter">
        <md-progress-spinner :md-diameter="100" :md-stroke="10" md-mode="indeterminate"></md-progress-spinner>
    </div>
    </div>
</template>
<script>
import db from '../firebase/init.js'
import firebase from 'firebase'
import { validationMixin } from 'vuelidate'
  import {
    required
  } from 'vuelidate/lib/validators'
export default {
    name:'applyleave',
    data(){
        return{
            form:{
                selectedToDate:null,
                selectedFromDate:null,
                type_of_leave:'',
                remark:null,
            },
            disabledDates: date => {
                    const day = date.getDay()

                    return day === 6 || day === 0
                },
            dispalert:false,
            sending: false,
            message: null,
            employee:[],
            leaves:[],
            loadedData:false,
            dayDifferece:null,
            testleavecheck:0
        }
        
    },
    validations: {
      form: {
        selectedToDate: {
          required,
        },
        selectedFromDate: {
          required,
        },
        
      }
    },
    methods:{
      dayDiff(startdate, enddate) {
  let dayCount = 0;

  while(enddate >= startdate) {
    dayCount++;
    startdate.setDate(startdate.getDate() + 1);
  }
dayCount--;
return dayCount ; 
},
can_We_Apply(){
  this.sending = true
  this.dayDifferece=this.dayDiff(this.form.selectedFromDate,this.form.selectedToDate)
  console.log('in function')
 if("Casual"==this.form.Type_of_leave){
      console.log("in first if")
      this.testleavecheck=this.leaves.Casual_Leave
      if((this.testleavecheck-this.dayDifferece)>=0)
      {
        console.log("in check")
        db.collection("Leaves").doc(this.leaves.collection_Id).update({
                                Employees_id:this.leaves.Employees_id,
                                Casual_Leave:(this.testleavecheck-this.dayDifferece),
                                Sick_Leave:3,
                                Earned_Leave:3
                            }).then(()=>{
                             this.submit_Leave_Request ()
                            }).catch(error=>{
                      console.log(error.message)
                    })
      }
      else{
        console.log("You don't have enough balance leaves")
      }
 }
      else{
        console.log("Type of leave error")
        console.log((this.form.Type_of_leave))
     }
    
},
      submit_Leave_Request () {
        
        
        
        db.collection("Leave_requests").add({
                        Employees_id:this.employee.uid,
                        From_Date:this.form.selectedFromDate,
                        To_Date:this.form.selectedToDate,
                        Type_of_leave:this.form.type_of_leave,
                        Remarks:this.form.remark,
                        Status:"Pending"
                    }).then(()=>{
                        console.log("Addeed to firestore")
                        this.message="Leave request has been send to supervisor"
                               
                              // Instead of this timeout, here you can call your API
                              window.setTimeout(() => {
                                this.dispalert = true
                                this.sending = false
                                //this.clearForm()
                              }, 1500)  
                    })
                    .catch(error=>{
                      this.message=error.message
                    })
        
      }
    },
    beforeCreate(){
                firebase.auth().onAuthStateChanged(authUser=> {
                    if (authUser!= null) {
                                //console.log(authUser.uid)
                               let dbref=db.collection("Employees").where("Employees_id","==",authUser.uid)
                                 dbref.get().then(snapshot=>{
                                    snapshot.forEach(emp => {
                                 this.employee=emp.data()
                                 this.employee.uid=authUser.uid
                         });
                          let dbref1=db.collection("Leaves").where("Employees_id","==",this.employee.uid)
                                 dbref1.get().then(snapshot=>{
                                    snapshot.forEach(leave => {
                                        this.leaves=leave.data()
                                        this.leaves.collection_Id=leave.id
                                        console.log(this.leaves.collection_Id)
                                        this.loadedData=true
                                    })
                                    })


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
.mycenter{
    margin:64px auto; 
    width:500px!important;
  }
</style>