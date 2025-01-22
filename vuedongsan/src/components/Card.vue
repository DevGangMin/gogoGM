<template>
<!-- 상품 목록 -->
  <div v-for="(oneroom,i) in onerooms" :key="i">
    <img :src="onerooms[i].image" class="room-img">
    <h4 @click="openModal(i)">{{onerooms[i].title}}</h4>
    <p>{{onerooms[i].price}}₩</p>
    <button @click="reports[i]++">허위 매물 신고</button>
    <span class="align-right">신고수: {{reports[i]}}</span>
  </div>
</template>

<script>
export default {
  name: 'Card',
  props: {
    onerooms: Array,
    reports: Number,
    ismodalopen: Boolean,
  },
  methods: {
    openModal(){
      this.$emit('open'); //부모에게 모달을 열라는 커스텀 이벤트를 전송
    }
  }
}
</script>

<style>
.align-right {
    display: block; /* 블록 레벨 요소로 만들어 줄 전체 너비를 차지하게 함 */
    text-align: right; /* 텍스트 왼쪽 정렬 */
    width: 100%; /* 부모 요소의 전체 너비 사용 */
    margin-top: 10px; /* 상단 여백 추가 */
}
.room-img {
    width: 100%; /* 이미지가 컨테이너 너비를 꽉 채우도록 설정 */
    height: auto; /* 이미지의 비율을 유지 */
}
</style>