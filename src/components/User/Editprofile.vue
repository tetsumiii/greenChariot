<template>
    <div>
        <div class="bg">
            <Head v-bind:id="id" class="head"></Head>
            <div class="top">
                <p class="content">Your points: {{this.points}}</p>
            </div>
            <img alt="logo" src="../../assets/welcome.png">
            <div class="profile">
                <p class="title">Edit Your Profile!</p>
                <p class = "txt"> Name </p>
                <input type="text" placeholder="Name" v-model='name' required><br>
                <p class = "txt"> Username </p>
                <input type="text" placeholder="username" v-model="username" required><br>
                <p class = "txt"> Email </p>
                <input type="text" placeholder="email" v-model="email" required><br>
                <p class = "txt"> New Password (leave it blank if you do not want to change password) </p>
                <input type="password" placeholder="Password" v-model="password"><br>
                <p class = "txt"> Confirm Password </p>
                <input type="password" placeholder="Confirm Password" v-model="confirmpass"><br>
                <p class="title2">Upload your profile picture below!</p>
                <input type="file" accept="image/*" @change="previewimage">
                <button class="btn2" v-on:click="removepic()">Remove picture</button>
                <p class="title2">Preview Here:</p>
                <template v-if= "this.preview!=''"><img alt="profilepic" :src="this.preview" class='imgfluid'><br></template>
                <p><button class="btn" v-on:click="confirm();updated();">Confirm</button>
                <button class="btn" v-on:click="signout()">Sign out</button></p>
            </div>
        </div>
        <Footer></Footer>
    </div>
</template>

<script>
import Head from './Header.vue'
import db from '../../firebase.js'
import Footer from '../Footer.vue'
export default{
    components:{
        Head,
        Footer
    },
    data(){
        return{
            id:this.$route.query.id,
            points:0,
            name:'',
            username:'',
            email:'',
            oldemail:'',
            image:'',
            preview:'',
            password:'',
            confirmpass:''
        }
    },
    methods:{
        retrieve:function(){
            db.firestore().collection('users').doc(this.id).get().then(snapshot => {
                this.points=snapshot.data().points
                this.name=snapshot.data().name
                this.username=snapshot.data().username
                this.email=snapshot.data().email
                this.oldemail=snapshot.data().email
                this.image=snapshot.data().image
            })
        },
        confirm:function(){
            if (this.name=='' || this.username=='' || this.email==''){
                alert('You need to fill in all the inputs!')
            }else if (this.password!= this.confirmpass && this.confirmpass!=''){
                return
            }else if (this.password!='' && this.confirmpass==''){
                return
            }else{
                db.firestore().collection('users').doc(this.id).update({
                name:this.name,
                username:this.username,
                image:this.image
            }).then(() => {
                alert("Profile updated successfully!")
                this.$router.push({name:'Dashboard',query:{id:this.id}})
            })
        }
    },
        signout:function(){
            db.auth().signOut().then(() => {
                alert('You have successfully signed out!')
                this.$router.push({name:'signin',query:{id:this.id}})
            }).catch(error => console.log(error))
        },
        previewimage:function(event){
            this.preview= URL.createObjectURL(event.target.files[0]);
            const reader= new FileReader()
            reader.onload = event => {
                this.image=event.target.result
            }
            reader.readAsDataURL(event.target.files[0])
        },
        removepic:function(){
            db.firestore().collection('users').doc(this.id).update({
                image:''
            }).then(() => {
                alert('Profile picture successfully removed!')
                this.$router.push({name:'Dashboard',query:{id:this.id}})
            })
        },
        updated:function(){
            const user=db.auth().currentUser
            var success=true
            if (this.oldemail==this.email && this.password!='' && this.password==this.confirmpass){
                user.updatePassword(this.password).catch(error => {
                    console.log(error)
                    alert(error)
                    success=false
                }).then(() => {if(success==true){
                      alert("Password has been changed! Please login again.")
                      this.$router.push({name:'signin',query:{id:this.id}})
                }})
            }else if (this.confirmpass!='' && this.password!=this.confirmpass){
                alert("Your password confirmation is incorrect!")
            }else if (this.password!='' && this.confirmpass==''){
                alert("Please enter your password confirmation!")
            }else if (this.oldemail!=this.email && this.confirmpass=='' && this.password==''){
                user.updateEmail(this.email).catch(error => {
                    alert(error)
                    success=false
                }).then(() => {
                    if(success==true){
                        db.firestore().collection('users').doc(this.id).update({
                            email:this.email
                        }).then(() => {
                            alert("Email successfully updated! Please relogin!")
                            this.$router.push({name:'signin',query:{id:this.id}})
                        })
                    }
                })
            } else if (this.oldemail!=this.email && this.password!='' && this.confirmpass!=''){
                user.updateEmail(this.email).catch(error => {
                    alert(error)
                    success=false
                }).then(() => {
                    if (success){
                        user.updatePassword(this.password).then(() => {
                            db.firestore().collection('users').doc(this.id).update({
                                email:this.email
                            })
                        }).then( () => {
                            alert("Email and Password resetted! Please relogin again")
                            this.$router.push({name:'signin',query:{id:this.id}})
                        })
                    }
                })
            }
                        
        } 
    },
       
    created(){
        this.retrieve()
    }
    

}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css?family=Montserrat');
    .bg {
        background-color: #9AD5FF;
        padding-bottom: 25px;
        margin: 0px;
        width: 100%;
        min-height: auto;
    }
    .head {
        position: sticky;
        top: 0;
        position: -webkit-sticky;
    }
    .content{
        font-family: Montserrat;
        font-weight: bold;
        font-size: 35px;
        margin-left: 2%;
        animation: zoomIn ease 0.7s
    }
    @keyframes zoomIn {
        0% {transform: scale(0.7,0.7); opacity:0}
        100% {transform: scale(1,1); opacity:1}
    }
    .top {
        margin-right: 100px;
        margin-top:2%;
        margin-bottom: 2%;
        margin-left:2%;
        display:flex
    }
    .title {
        font-family: Inter;
        font-style: normal;
        font-weight: bold;
        font-size: 44px;
        line-height: 58px;
        text-align: center;
        color: #1C746F;
        margin: 15px;
    }
    .txt {
        font-family: Inter;
        font-size: 24px;
        color: #1C746F;
        margin-left: 8%;
        text-align: left;
    }
    .profile {
        background-color: #FFFFFF;
        margin-left: 55%;
        margin-right: 10%;
        height: 50%;
        width: 35%;
        justify-content: center;
        align-items: center;
        text-align: center;
        border-radius: 8px;
        padding: 10px;
        animation: zoomIn ease 0.7s
    }
    input {
        font-family: Montserrat;
        padding: 8px;
        width: 80%;
        margin: 3%;
        font-size: 22px;
        border-radius: 8px;
        border: 1px solid #E5E5E5;
        height: 10%
    }
    .btn {
        background: #2D8F8A;
        border: none;
        border-radius: 8px;
        font-family: Montserrat;
        font-style: normal;
        font-weight: bold;
        font-size: 20px;
        align-items: center;
        text-align: center;
        color: #FFFFFF;
        width: 37%;
        height: 50px;
        text-align: center;
        cursor: pointer;
        margin:7px;
        transition-duration: 0.4s;
    }
    .btn:hover{
        background:#1C746F;
    }
    .picture{
        height:10%
    }
    img {
        float: left;
        width: 40%;
        justify-content: center;
        margin-left: 30px
    }
    .title2 {
        font-family: Inter;
        font-style: normal;
        font-weight: bold;
        font-size: 25px;
        line-height: 58px;
        text-align: center;
        color: #1C746F;
        margin: 15px;
    }
    .imgfluid{
        justify-content:center;
        margin-left:30%;
        height:150px
    
    }
    .btn2 {
        background: #2D8F8A;
        border: none;
        border-radius: 8px;
        font-family: Montserrat;
        font-style: normal;
        font-weight: bold;
        font-size: 20px;
        align-items: center;
        text-align: center;
        color: #FFFFFF;
        width: 55%;
        height: 50px;
        text-align: center;
        cursor: pointer;
        margin:7px;
        border:none;
        transition-duration: 0.4s;
    }
    .btn2:hover{
        background:#1C746F;
    }

    



</style>