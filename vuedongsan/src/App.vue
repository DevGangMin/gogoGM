<template>
  <div>
    <!-- 모달창 내용 -->
    <div class="black-bg" v-if="ismodalopen == true">
      <div class="white-bg">
        <h4>상세페이지</h4>
        <p>상세페이지내용임</p>
        <button @click="ismodalopen = false">닫기</button>
      </div>
    </div>

    <!-- 상단 메뉴 바 -->
    <div class="menu">
      <a v-for="menu in menus" :key="menu">{{ menu }}</a>
    </div>

    <!-- 상품 목록 -->
    <div v-for="(oneroom,i) in onerooms" :key="i">
      <img :src="onerooms[i].image" class="room-img">
      <h4 @click="isopenmodal = true">{{onerooms[i].title}}</h4>
      <p>{{onerooms[i].price}}₩</p>
    </div>

  </div>
</template>

<script>
import data from './assets/oneroom.js';
export default {
  name : 'App',
  components : {
  },
  data(){
    return {
      onerooms : data,
      menus : ['Home', 'Shop', 'About'],
      reports : [0, 0, 0],
      products : ['역삼동원룸','천호동원룸','마포구원룸'],
      ismodalopen : false,
    }
  }
}
</script>

<style>
.menu {
  background : darkslateblue;
  padding : 15px;
  border-radius : 5px;
}
.menu a {
  color : white;
  padding : 10px;
}
body {
  margin : 0;
}
div {
  box-sizing: border-box;
}
.black-bg {
  width: 100%; height:100%;
  background: rgba(0,0,0,0.5);
  position: fixed; padding: 20px;
}
.white-bg {
  width: 100%; background: white;
  border-radius: 8px;
  padding: 20px;
} 
</style> 