<template>
  <div>
    <img alt="Vue logo" src="./assets/vuedongsan.png" width="12.4%">

    <!-- 모달 컴포넌트에 원룸들, 누른거, 모달창열렸니, closemodal 메소드를 넣어줌 -->
    <modal :원룸들="원룸들" :누른거="누른거" :모달창열렸니="모달창열렸니" :closemodal="closemodal"/>

    <div class="menu">
      <a v-for="(name, i) in menus" :key="i">{{ name }}</a>
    </div>

    <discount />

    <div v-for="(data, i) in 원룸들" :key="i">
      <img :src="data.image" class="room-img">
      <h4 @click="모달창열렸니 = true; 누른거 = i"> {{ data.title }} </h4>
      <p>{{ data.price }}원</p>
      <button @click="신고수[i]++">허위매물신고</button>
      <span>신고수 : {{ 신고수[i] }}</span> <br><br>
    </div>
  </div>
</template>

<script>
import discount from './components/Discount.vue';
import data from './oneroom.js';
import modal from './components/Modal.vue'
export default {
  name: 'App',
  data() { // 앵간하면 데이터들은 밑으로 보낼 땐 props만 쓰면 되는데 위로 올리는건 불편하니까 그냥 최상위 컴포넌트에 데이터 다 집어넣자.
    return {
      menus: ['Home', 'Shop', 'About'],
      신고수: [0, 0, 0, 0, 0, 0],
      모달창열렸니: false,
      원룸들: data,
      누른거: 0,
    }
  },
  methods: {
    closemodal() {
      this.모달창열렸니 = false
    },

    handleEscKey(event) {
      if (event.key === "Escape") {
        this.closemodal(); // ESC 키가 눌리면 모달 닫기
      }
    }
  },
  mounted() {
    window.addEventListener('keydown', this.handleEscKey);
  },
  unmounted() {
    window.removeEventListener('keydown', this.handleEscKey);
  },
  components: {
    discount: discount,
    modal : modal,
  }
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

body {
  margin: 0;
}

div {
  box-sizing: border-box;
}

.menu {
  background: darkslateblue;
  padding: 15px;
  border-radius: 5px;
}

.menu a {
  color: white;
  padding: 10px;
}

.black-bg {
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  position: fixed;
  padding: 20px;
}

.white-bg {
  width: 100%;
  background: white;
  border-radius: 8px;
  padding: 20px;
}

.room-img {
  width: 100%;
  height: 100%;
}

.Discount {
  background: #eee;
  padding: 10px;
  margin: 10px;
  border-radius: 5px;
}
</style>
