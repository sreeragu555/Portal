<template>
    <div >
        <div class="container login">
            <form class="card-panel" @submit.prevent="Login">
                <h2 class="center">Login</h2>
                <div class="field">
                <label for="email">Email address</label>
                <input type="text" @change="ValidEmail" id="Email" v-model="form.Email" name="Email">
                </div>
                <div class="center" v-if="FeedbackEmail">
                    <span class="red-text text-darken-2">{{FeedbackEmail}}</span>
                </div>
                <div class="field">
                <label for="Password">Password</label>
                <input type="password" @change="ValidPwd" v-model="form.Password" name="Password">
                </div>
                <div class="center" v-if="Feedbackpwd">
                    <span class="red-text text-darken-2">{{Feedbackpwd}}</span>
                </div>
                <div class="field center">
                    <button class="btn deep-purple-blue">Login</button>
                </div>
                <div class="center" v-if="Feedback">
                    <span class="red-text text-darken-2">{{Feedback}}</span>
                </div>
            </form>
        </div>
    </div>
</template>
<script>
import firebase from 'firebase'
import { required, minLength, email } from 'vuelidate/lib/validators'
export default {
    name:"Login",
    data(){
        return{
            form:{
                Email:null,
                Password:null,
            },
            FeedbackEmail:null,
            Feedbackpwd:null,
            Feedback:null
        }
    },
    validations: {
        form:{
        Email:{
            required,
            email
        },
    Password: {
      required,
      minLength: minLength(6)
    }
        }
},
methods:{
    ValidEmail(){
        if(this.$v.form.Email.$invalid) {
        this.FeedbackEmail="Please enter a valid Email"
    }
    //else{
   //     document.getElementById("Email").style.borderBottomColor ="Blue"
   // }
    },
    ValidPwd(){
        if(this.$v.form.Password.$invalid) {
        this.FeedbackEmail="Please enter a valid Password"
    }
    },
    Login(){
        if(!this.$v.form.$invalid){
            firebase.auth().signInWithEmailAndPassword(this.form.Email,this.form.Password)
            .then(cred=>{
                console.log(cred.user.uid)
            })
           .catch(function(error) {
                var message=error.message
                alert(message)
                //this.Feedback=message
           })
         }
        else {
            console.log("Validation error")
       }
}
}
}
</script>
<style>
.login{
    max-width: 400px;
    margin-top: 60px;
}
.login h2{
    font-size: 2.4em;
}
.login .field{
    margin-bottom: 16px;
}
</style>
