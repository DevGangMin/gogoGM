<template>
  <div>

    <!-- 모달창 컴포넌트 불러오기 -->
    <Modal :onerooms="onerooms" :clicked="clicked" :ismodalopen="ismodalopen" @close="ismodalopen = false"/>

    <!-- 할인 배너 컴포넌트 불러오기 -->
    <Discount/>

    <!-- 상단 메뉴 바 -->
    <div class="menu">
      <a v-for="menu in menus" :key="menu">{{ menu }}</a>
    </div>

    <!-- 상품 목록 -->
    <Card :onerooms="onerooms" :reports="reports" :ismodalopen="ismodalopen" @open="ismodalopen = true"/>

  </div>
</template>

<script>
import data from './assets/oneroom.js';
import Discount from './components/Discount.vue';
import Modal from './components/Modal.vue';
import Card from './components/Card.vue';

export default {
  name : 'App',
  components : {
    Discount,
    Modal,
    Card,
  },
  data(){
    return {
      onerooms : data,
      menus : ['Home', 'Shop', 'About'],
      reports : [0, 0, 0, 0, 0, 0],
      products : ['역삼동원룸','천호동원룸','마포구원룸'],
      ismodalopen : false,
      clicked : 0,
    }
  },
  methods: {
    openModal(index) {
      this.ismodalopen = true;
      this.clicked = index;
    }
  }
}
</script>

<style>
.room-img {
  background : #ed2;
  padding: auto;
  border-radius: 5px;
  width: 100%;
}
.menu {
  background : darkslateblue;
  padding : 15px;
  border-radius : 5px;
  text-align: center;
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
.discount {
  background: #eee;
  padding: 10px;
  margin: 10px;
  border-radius: 5px;
  text-align: center;
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