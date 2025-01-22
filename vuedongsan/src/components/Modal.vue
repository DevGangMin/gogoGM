<template>
  <!-- 모달창 내용 -->
  <div class="black-bg" v-if="ismodalopen">
    <div class="white-bg">
      <img :src="onerooms[clicked].image" style="width: 100%; height: auto;">
      <h4>{{ onerooms[clicked].title }}</h4>
      <p>{{ onerooms[clicked].content }}</p>
      <p>{{ onerooms[clicked].price }}₩</p>
      <input>
      <button @click="closeModal">구매</button>
      <button @click="closeModal">닫기</button>
    </div>
  </div>  
</template>

<script>
export default {
  name: 'Modal',
  props: {
    onerooms: Array,
    clicked: Number,
    ismodalopen: Boolean,
  },
  methods: {
    closeModal() {
      this.$emit('close');  // 부모에게 모달을 닫으라는 이벤트를 전송
    }
  }
}
</script>
<style>
</style>