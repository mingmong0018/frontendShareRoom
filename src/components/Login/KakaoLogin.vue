<template>
  <KakaoLogin
    :api-key=kakaoLoginKey
    image="kakao_account_login_btn_medium_narrow_ov"
    style="margin-bottom:20px;"
    :on-success=onSuccess
    :on-failure=onFailure
/>
</template>

<script>
import router from '@/router/index.ts'
import store from '@/store/index.ts'
import axios from'axios'
import KakaoLogin from 'vue-kakao-login'
const onSuccess = (data) => {
  console.log(data)
  const params=new URLSearchParams({
                        accessToken : data.access_token,
                        state : 'kakao'
                    });
                    axios.post(process.env.VUE_APP_AXIOS_URL+'/login',params).then(res=>{
                        const tmp=String(res.data).split(",");
                        console.log(tmp);
                        const accessToken=tmp[0];
                        const userName=tmp[1];
                        const userId=tmp[2];
                        
                        store.dispatch("Login/LOGIN", { accessToken, userName, userId })
                        router.go(router.currentRoute);
                        
                    
                    })
  console.log("success")
}
const onFailure = (data) => {
  console.log(data)
  console.log("failure")
}
export default {
    data() {
      return {
        kakaoLoginKey: process.env.VUE_APP_KAKAO_LOGIN_KEY
      }
    },
    components:{
        KakaoLogin
    },
    methods:{
        onSuccess,
        onFailure,
    }
}
</script>

<style scoped src="@/static/css/loginModal.css">

</style>