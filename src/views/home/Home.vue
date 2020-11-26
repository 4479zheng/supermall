<template>
  <div id="home">
    <nav-bar class="home-nav">
      <template v-slot:center>
        <div>购物街</div>
      </template>
    </nav-bar>
    <home-swiper :banners="banners"/>
    <recommen-view :recommends="recommends"/>
    <feature-view/>
    <tab-control class="tab-control" :titles="title" @tabClick="tabClick"/>
    <goods-list :goods="showGoods"></goods-list>
  </div>
</template>

<script>
import NavBar from 'components/common/navbar/NavBar'
import TabControl from 'components/content/tabControl/TabControl'
import GoodsList from 'components/content/goods/GoodsList'

import HomeSwiper from './childComps/HomeSwiper'
import RecommenView from './childComps/RecommenView'
import FeatureView from './childComps/FeatureView'

import {getHomeMultidata,getHomeGoods} from 'network/home'
export default {
  name:'Home',
  data(){
    return{
      banners: [],
      recommends: [],
      title: ['流行','新款','精选'],
      goods:{
        'pop': {page: 0, list:[]},
        'new': {page: 0, list:[]},
        'sell': {page: 0, list:[]},
      },
      currenType: 'pop'
    }
  },
  components:{
    NavBar,
    TabControl,
    GoodsList,
    HomeSwiper,
    RecommenView,
    FeatureView,
    
  },
  created() {
    // 请求多个数据
    this.getHomeMultidata();
    //  请求商品数据
    this.getHomeGoods('pop');
    this.getHomeGoods('new');
    this.getHomeGoods('sell');
  },
  methods:{
    // 事件监听的方法
    tabClick(index){
      // this.goods[index]
      switch(index){
        case 0:
          this.currenType = 'pop';
          break;
        case 1:
          this.currenType = 'new';
          break;
        case 2:
          this.currenType = 'sell';
          break;
      }
    },
    //网络请求相关的
    getHomeMultidata(){
       getHomeMultidata().then(res =>{
      console.log(res);
      // this.result = res;
      this.banners = res.data.banner.list;
      this.recommends = res.data.recommend.list;
      })
    },
    getHomeGoods(type){
        const page = this.goods[type].page+1;
       getHomeGoods(type,page).then(res =>{
         this.goods[type].list.push(...res.data.list);
         this.goods[type].page+=1;
         console.log(res);
    })
    },
  },
  computed:{
    showGoods(){
      return this.goods[this.currenType].list
    }
  }
}
</script>

<style>
#home{
  padding-top: 44px;
}
.home-nav{
  background-color: var(--color-tint);
  color: white;
  position: fixed;
  left: 0;
  right: 0;
  top: 0%;
  z-index: 9;
}
.tab-control{
  position: sticky;
  top: 44px;
  z-index: 9;
}
</style>