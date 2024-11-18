<template>
  <div>
    <img alt="Vue logo" src="./assets/vuedongsan.png" width="12.4%">

    <modal />
    
    <div class="menu">
      <a v-for="(name, i) in menus" :key="i">{{ name }}</a>
    </div>

    <discount />

    <div v-for="(data, i) in 원룸들" :key="i">
      <img :src="data.image" class="room-img">
      <h4 @click="openmodal(i)"> {{ data.title }} </h4>
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
  data() {
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
    openmodal(index) {
      this.누른거 = index; // 누른거 순서를 알기 위해 '누른거'를 index로 저장함. 아 근데 이렇게 쓰면 나중에 오픈 모달을 다른 인덱스를 사용하는 이벤트에는 못쓴다는 단점이 있음.
      this.모달창열렸니 = true; // 귀찮으니까 오픈모달 로직 안에 두개 다 넣음. 어차피 모달창 열 때 누른거 몇 번째 껀지 정보 알아야 되니까.
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
