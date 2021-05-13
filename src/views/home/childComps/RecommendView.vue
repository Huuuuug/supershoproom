<template>
  <div class="recommend">
      <div v-for="item in recommends" :key="item.acm" class="recommend-view">
          <a :href="item.link">
              <img :src="item.image" alt="">
              <div>{{item.title}}</div>
          </a>
      </div>
  </div>
</template>

<script>
export default {
    name:"RecommendView",
    props:{
        recommends:{
            type:Array,
            default() {
                return []
            }
        }
    }
}
</script>

<style>
.recommend {
    display: flex;
    width: 100%;
    text-align: center;
    font-size: 12px;
    padding: 10px 0 20px;
    border-bottom: 10px solid #eee;
}
.recommend-view {
    flex: 1;
}
.recommend-view img {
    width: 75px;
    height: 75px;
    margin-bottom: 10px;
}
</style>