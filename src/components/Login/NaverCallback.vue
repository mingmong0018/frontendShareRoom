<template>
    <div>
        <div id="naverIdLogin" style="display: none"></div>
    </div>
</template>

<script>
import router from '@/router/index.ts'
import store from '@/store/index.ts'
import axios from'axios'
export default {
    mounted(){
        const naverLogin = new window.naver.LoginWithNaverId({
        clientId: process.env.VUE_APP_NAVER_CLIENT_ID,
        callbackUrl: `http://shareroom-frontend.s3-website.ap-northeast-2.amazonaws.com/callback`,
        isPopup: false ,
        loginButton: {
            color: 'green',
            type: 3,
            height: 60
        }
        })
        naverLogin.init();
        naverLogin.getLoginStatus(function(status) {
            if (status) {
                console.log(naverLogin)
                const params=new URLSearchParams({
                        accessToken : naverLogin.accessToken.accessToken,
                        state : 'naver'
                    });
                    axios.post(process.env.VUE_APP_AXIOS_URL+'/login',params).then(res=>{
                        const tmp=String(res.data).split(",");
                        console.log(tmp);
                        const accessToken=tmp[0];

                        const userName=tmp[1];
                        const userId=tmp[2];
                        
                        store.dispatch("Login/LOGIN", { accessToken, userName, userId })
                        router.push('/')
                        
                        
                        
                    
                    })
            console.log("success")
            } else {
                console.log('AccessToken이 올바르지 않습니다.')
            }
            })
    },

}
</script>

<style>

</style>