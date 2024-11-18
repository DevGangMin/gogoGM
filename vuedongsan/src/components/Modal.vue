<template>
  <div class="black-bg" v-if="모달창열렸니 == true">
    <div class="white-bg">
      <img :src="원룸들[누른거].image" class="room-img">
      <h4>{{ 원룸들[누른거].title }}</h4>
      <p>{{ 원룸들[누른거].content }}</p>
      <p>{{ 원룸들[누른거].price }}원</p>
      <button @click="closemodal">닫기</button> <!-- props는 read-only이기 때문에, 모달창열렸니 = false 마냥 등호를 지 멋대로 바꾸면 안되기에, closemodal 함수로 하여금 상태를 boolean으로 변경함.-->
    </div>
  </div>
</template>

<script>
export default {
  name: 'Modal',
  props: {
    원룸들: Array,
    누른거: Number,
    모달창열렸니: Boolean,
    closemodal: Boolean,
  }
}
</script>

<style>

</style>