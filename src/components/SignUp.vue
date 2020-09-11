<template>
    <div >
        <div class="container SignUp">
            <form class="card-panel" @submit.prevent="SignUp">
                <h2 class="center">Sign Up</h2>
                <div class="field">
                <label for="email">Email address</label>
                <input type="text" v-model="form.Email" name="Email">
                </div>
                <div class="field">
                <label for="Name">Name</label>
                <input type="text" v-model="form.Name" name="Name">
                </div>
                <div class="field">
                <label for="Phone number">Phone number</label>
                <input type="text" v-model="form.phone" name="Phone number">
                </div>
                <div class="field">
                <label for="Password">Password</label>
                <input type="password" v-model="form.Password" name="Password">
                </div>
                <div class="field">
                <label for="ConfPassword">Confirm Password</label>
                <input type="password" v-model="form.confpwd" name="ConfPassword">
                </div>
                <div class="field center">
                    <button class="btn deep-purple-blue">SignUp</button>
                </div>
            </form>
        </div>
    </div>
</template>
<script>
import db from '@/Firebase/init'
import firebase from 'firebase'
import { required, minLength, email,sameAs } from 'vuelidate/lib/validators'
export default {
    name:"SignUp",
    data(){
        return{
            form:{
            Email:null,
            Name:null,
            Password:null,
            phone:null,
            confpwd:null,
            }
        }
    },validations: {
        form:{
        Email:{
            required,
            email
        },

    Password: {
      required,
      minLength: minLength(6)
    },
    confpwd: {
      sameAsPassword: sameAs('Password')
    }
        }
    },
methods:
{
    SignUp(){
        if(!this.$v.form.$invalid)
        {
            
            firebase.auth().createUserWithEmailAndPassword(this.form.Email, this.form.Password)
            .catch(console.error())
            .then(cred=>{
                console.log("added to AUTH")
                db.collection("Employees").add({
                    Employees_id:cred.user.uid,
                    Name:this.form.Name,
                    Phone_Number:this.form.phone
                }).then(()=>{
                    console.log("Addeed to firestore")
                })
            })
        }
        else{
            console.log("Error before AUTH()")
        }
    }
}
}
</script>
<style>
.SignUp{
    max-width: 600px;
    margin-top: 60px;
}
.SignUp h2{
    font-size: 2.4em;
}
.SignUp .field{
    margin-bottom: 16px;
}
</style>