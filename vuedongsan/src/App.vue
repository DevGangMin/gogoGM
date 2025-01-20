<template>
  <div>
    <div class="menu">
      <a v-for="menu in menus" :key="menu">{{ menu }}</a>
    </div>
    <div>
      <h4>{{products[0]}}</h4>
      <p>50만원</p>
      <button @click="reports[0]++">허위매물신고</button>
      <span>신고수 : {{reports[0]}}</span>
    </div>
    <div>
      <h4>{{products[1]}}</h4>
      <p>60만원</p>
      <button @click="reports[1]++">허위매물신고</button>
      <span>신고수 : {{reports[1]}}</span>
    </div>
    <div>
      <h4>{{products[2]}}</h4>
      <p>70만원</p>
      <button @click="reports[2]++">허위매물신고</button>
      <span>신고수 : {{reports[2]}}</span>
    </div>
  </div>
</template>

<script>
export default {
  name : 'App',
  components : {
  },
  data(){
    return {
      menus : ['Home', 'Shop', 'About'],
      products : ['역삼동원룸','천호동원룸','마포구원룸'],
      reports : [0, 0, 0],
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
</style> 