<template>
    <div>
        <div class="bg">
            <Head v-bind:id="id" class="head"></Head>
            <div class = "item">
                <div class="rewards">
                    <img alt="successful" src="../../assets/successful.png">
                </div>
                <div class="content">
                    <p class="zoom">Hooray!</p>
                    <p class="title2">You get 500 points!</p>
                    <p class="txt">Your new points: {{this.newpoints}}</p>
                    <p class="txt">Your old points: {{this.oldpoints}}</p>
                    <button class="btn" v-on:click="direct()">Rewards</button>
                </div>
            </div>
        </div>
        <Footer></Footer>
    </div>
</template>

<script>
import Head from './Header.vue'
import Footer from '../Footer.vue'
export default{
    components: {
        Head,
        Footer
    },
    data(){
        return{
            id:this.$route.query.id,
            name:this.$route.query.name,
            oldpoints:this.$route.query.oldpoints,
            newpoints:Number(this.$route.query.oldpoints) + 500,
            shopId: this.$route.query.shopId
        }
    },
    methods:{
        direct:function(){
            this.$router.push({name:'scooprewards',query:{id:this.id,name:this.name, shopId:this.shopId}})
        }
    },
}
</script>

<style scoped>
    @import url('https://fonts.googleapis.com/css?family=Montserrat');
    .bg {
        background-color: #9AD5FF;
        padding: 0px;
        margin: 0px;
        width: 100%;
        min-height: 100vh;
    }
    .head {
        position: sticky;
        top: 0;
        position: -webkit-sticky;
    }
    img {
        float: left;
        width: 40%;
        justify-content: center;
        margin-left: 7%;
        float:left;
        animation: zoomIn ease 1s
    }
    @keyframes zoomIn {
        0% {
            transform: scale(0.2,0.2)
        }
        100% {transform: scale(1,1)}
    }
    .title {
        font-family: Montserrat;
        font-style: normal;
        font-weight: bold;
        font-size: 30px;
        margin: 10px;
        padding:10px
    }
    .zoom {
        font-family: Montserrat;
        font-style: normal;
        font-weight: bold;
        font-size: 64px;
        margin: 10px;
        margin-bottom: 0px;
        margin-top: 50px;
        animation: large ease 1s
    }
    @keyframes large {
        0% {
            transform: scale(0.5,0.5);
        }
        100% {transform: scale(1,1)}
    }
    .rewards {
        margin-top: 10px;
    }
    .content {
        margin-left: 55%;
        margin-right: 10%;
        width: 40%;
        justify-content: center;
        align-items: center;
        text-align: center;
        border-radius: 8px;
        padding: 10px;
    }
    .title2 {
        font-family: Montserrat;
        font-weight: bold;
        font-size: 48px;
    }
    .txt {
        font-family: Montserrat;
        font-size: 30px;
        margin-top: 20px;
    }
    .btn {
        background: #2D8F8A;
        border-radius: 8px;
        font-family: Montserrat;
        font-style: normal;
        font-weight: bold;
        font-size: 20px;
        line-height: 24px;
        text-align: center;
        color: #FFFFFF;
        width: 30%;
        height: 50px;
        margin: 20px;
        cursor: pointer;
    }
    .item {
        margin-top: 2%
    }
</style>