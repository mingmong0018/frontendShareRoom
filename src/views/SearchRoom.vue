<template>
  <div class="list-room-wrap">
    <roomMap v-if="this.rooms.length>0" :rooms="rooms" :searchTxt="searchTxt" @changeList="newRoom"/>
    <roomList :roomList="roomList"/>
    <registerRoomBtn/>
  </div>
</template>

<script>
import axios from'axios'
import roomList from "@/components/Room/roomList.vue"
import roomMap from "@/components/Map/roomMap.vue"
import registerRoomBtn from "@/components/Buttons/registerRoomBtn.vue"
export default {
  name: 'SearchRoom',
  props: {
    mainSearchText: {
      type: String
    },
    tag: {
      type: String
    }
  },
  components:{
    roomList,
    roomMap,
    registerRoomBtn
  },
  data() {
    return {
      rooms:[], // 서버에서 받아온 방 리스트
      roomList:[],  // 컴포넌트에 보내줄 리스트
      searchTxt: this.mainSearchText,
    }
  },
  created() {
    if(this.tag!=null) {
      axios.get(process.env.VUE_APP_AXIOS_URL+'/tagListRoom',
      {
        params:{tag:this.tag}
      }
      ).then((response)=>{
        this.rooms=response.data;
        alert("해시태그 '"+this.tag+"' (으)로 검색한 결과입니다. 새로고침하면 전체 목록으로 변경됩니다 :)");
      });
    }else {
      axios.get(process.env.VUE_APP_AXIOS_URL+'/listRoom'
      ).then((response)=>{
        this.rooms=response.data;
      });
    }
  },
  methods: {
    newRoom(roomList) {
      this.roomList=roomList;
    },
  }
}
</script>

<style scoped src="@/static/css/roomMap.css">
</style>
<style scoped src="@/static/css/searchBox.css">
</style>
<style>
.list-room-wrap {
  padding-top:60px;
  height:100vh;
}
#customOverlay {
  width:210px;
  height:250px;
  background:white;
  border-radius:15px;
  box-shadow:0 0 20px 1px #bdbdbd;
  overflow:hidden;
}
#overlay-image {
  width:210px;
  height:150px;
}
#overlay-image img{
  width:210px;
  height:150px;
}
#overlay-content {
  width:210px;
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
  padding:5px 10px;
}
.room-title {
    font-weight:700;
    font-size:1em;
}
.room-region {
    font-size:0.7em;
    color:#6e6e6e;
    margin-bottom:10px;
}
</style>
