<template>
  <div id="home">
    <nav-bar class="home_nav"><div slot="center">购物街</div></nav-bar>
    <scroll class="content" ref="scroll"
            :probe-type="3"
            @scroll="contentScroll"
            :pull-up-load="true"
            @pullingUp="pullingUp">
       <home-Swiper :banners ="banners"/>
    <recommend-view :recommends="recommends"></recommend-view>
    <feature-view/>
    <tab-control :titles="['流行', '新款', '精选']" class="tab-control"
                  @tabClick="tabClick"/>
    <goods-list :goods="showGoods"></goods-list>
    </scroll>
    <back-top @click.native="backClick" v-show="isBackTop"/>

  </div>
</template>

<script>
import NavBar from 'components/common/navbar/NavBar'
import Scroll from 'components/common/scroll/Scroll'

import BackTop from 'components/content/backtop/BackTop'
import TabControl from 'components/content/tabControl/TabControl.vue'
import GoodsList from 'components/content/goods/GoodsList.vue'

import HomeSwiper from './childComps/HomeSwiper'
import RecommendView from './childComps/RecommendView'
import FeatureView from './childComps/FeatureView'

import {getHomeMultidata, getHomeGoods} from 'network/home'

export default {
  name:"Home",
  components:{
    NavBar,
    HomeSwiper,
    RecommendView,
    FeatureView,
    TabControl,
    GoodsList,
    Scroll,
    BackTop
  },
  data() {
    return {
      banners: [],
      recommends:[],
      goods: {
        'pop':{page: 0, list: []},
        'new':{page: 0, list: []},
        'sell':{page: 0, list: []},
      },
      currentType: 'pop',
      isBackTop: false,
    }
  },
  created() {
    //1、请求多个数据
    this.getHomeMultidata();
    //2、请求商品数据
    this.getHomeGoods('pop');
    this.getHomeGoods('new');
    this.getHomeGoods('sell');
  },
  computed:{
    showGoods() {
      return this.goods[this.currentType].list
    }
  },
  methods: {
    // 事件监听相关方法
    tabClick(index){
      switch(index){
        case 0:
          this.currentType = 'pop';
          break
        case 1:
          this.currentType = 'new';
          break
        case 2:
          this.currentType = 'sell';
      } 
    },
    backClick(){
      this.$refs.scroll.scrollTo(0, 0, 1000);
    },
    contentScroll(position){
      // console.log(position)
      this.isBackTop = Math.abs(position.y) > 1000
    },
    pullingUp(){
      this.getHomeGoods(this.currentType)

      this.$refs.scroll.scroll.refresh()
    },

    // 网络请求相关方法
    getHomeMultidata() {
      getHomeMultidata().then(res => {
      // console.log(res)
      this.banners = res.data.banner.list;
      this.recommends = res.data.recommend.list;
    })
    },
    getHomeGoods(type) {
      const page = this.goods[type].page + 1
      getHomeGoods(type,page).then(res => {
      // console.log(res)
      this.goods[type].list.push(...res.data.list)
      this.goods[type].page += 1

      this.$refs.scroll.finishPullUp()
    })
    }
  },
}
</script>

<style scoped>
#home {
  position: relative;
  padding-top: 44px;
  height: 100vh;
}
.home_nav {
  background-color: var(--color-tint);
  color: #fff;

  position: fixed;
    left: 0;
    right: 0;
    top: 0;
    z-index: 9;
}
.tab-control {
  position: sticky;
  top: 44px;
  z-index: 9;
}
.content {
  position:absolute;
  height: calc(100%-98px);
  overflow: hidden;
  top: 44px;
  bottom: 49px;
  left: 0;
  right: 0;
}
</style>