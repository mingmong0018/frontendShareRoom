<template>
    <div id="wish-button" :class="roomId" @click="changWishStatus">
        <div id="wish-icon" v-if="this.wish==true" title="찜 해제">
            <img src="wish_on.png" width="22">
        </div>
        <div id="wish-icon" v-else title="찜하기">
            <img src="wish_off.png" width="22">
        </div>
    </div>
</template>

<script>
import axios from 'axios'
export default {
    props: {
        roomId: String
    },
    data() {
        return {
            userId:this.$store.state.Login.userId,
            wish:false,
            message:''
        }
    },
    created() {
        if(this.userId!=null) {
            this.getWish();
        }
    },
    methods: {
        getWish() {
            const params=new URLSearchParams({
                id:this.userId,
                roomId:this.roomId
            });
            axios({
                url: process.env.VUE_APP_AXIOS_URL+'/wish', 
                method: "GET",
                params: params,
                headers:{
                    Authorization : "Bearer "+this.$store.state.Login.accessToken
                }
            }).then(res=>{
                this.wish=res.data;
            });
        },
        changWishStatus() {
            if(this.userId==null) {
                this.$bvModal.show('loginModal')
            }else {
                const params=new URLSearchParams({
                    id:this.userId,
                    roomId:this.roomId
                });
                if(this.wish==false) {
                    axios({
                        url: process.env.VUE_APP_AXIOS_URL+'/wish', 
                        method: "POST",
                        params: params,
                        headers:{
                            Authorization : "Bearer "+this.$store.state.Login.accessToken
                        }
                    }).then(res=>{
                        if(res.data!='') {
                            this.getWish();
                            this.message='찜 목록에 추가되었습니다'
                            this.toast('b-toaster-top-center');
                            console.log("insert",this.wish);
                        }else{
                            this.$store.dispatch("Login/LOGOUTCLICK")
                        } 
                    }).catch(( err ) => {
                        console.log( err );
                        throw err;
                    });
                }else {
                    axios({
                        url: process.env.VUE_APP_AXIOS_URL+'/wish', 
                        method: "DELETE",
                        params: params,
                        headers:{
                            Authorization : "Bearer "+this.$store.state.Login.accessToken
                        }
                    }).then(res => {
                        if(res.data!='') {
                            this.message='찜 목록에서 삭제되었습니다';
                            this.toast('b-toaster-top-center');
                            this.getWish();
                            this.$emit('deleteWish');
                        }else{
                            this.$store.dispatch("Login/LOGOUTCLICK")
                        } 
                    }).catch(( err ) => {
                        console.log( err );
                        throw err;
                    });
                }
            }  
        },
        toast(toaster, append = false) {
            this.counter++
            this.$bvToast.toast(this.message, {
                toaster: toaster,
                appendToast: append,
                autoHideDelay: 1500,
                noCloseButton: true
            })
        }
    }
}
</script>

<style>
    .b-toaster-slot {
        top: 5rem !important;
    }
</style>