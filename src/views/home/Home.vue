<template>
  <div id="home">
    <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
    <scroll :probe-type="3"
            :pull-up-load="true"
            class="content"
            :data="[showGoodsList]"
            ref="scroll">
    <swiper :banners="banners"/>
    <recommend-menu :recommends="recommends"/>
    <hot/>
    <tab-control ref="contentTab" class="tab-control" @tabClick="tabClick"></tab-control>
    <goods-list :goods="showGoodsList"></goods-list>
    
    </scroll>
  </div>
</template>

<script>
  import NavBar from 'components/common/navbar/NavBar'
  // import Scroll from 'components/common/scroll/Scroll'
  import TabControl from 'components/content/tabControl/TabControl'
  import Scroll from 'components/common/scroll/Scroll'

  import Swiper from './subComponents/HomeSwiper'
  import RecommendMenu from './subComponents/RecommendMenu'
  import Hot from './subComponents/Hot'
  import GoodsList from '../../components/content/goods/GoodsList'

  import {getMultiData, getProductData} from "network/home";
  import {TOP_DISTANCE, POP, NEW, SELL} from "common/const";

  export default {
    name: "Home",
    components: {
      NavBar,
      Swiper,
      RecommendMenu,
      Hot,
      TabControl,
      Scroll,
      GoodsList,
      // Scroll,
    },

    data() {
      return {
        banners: [],
        recommends: [],
        goods: {
          'pop': {page: 1, list: []},
          'new': {page: 1, list: []},
          'sell': {page: 1, list: []},
        },
        currentType: POP,
        showTabControl: false,
        tabOffsetTop: 0,
        saveY: 0
      }
    },
    computed: {
      showGoodsList() {
        return this.goods[this.currentType].list
      }
    },
    created() {
      // 1.请求轮播等数据
      this._getMultiData()
      this._getProductData(POP)
      this._getProductData(NEW)
      this._getProductData(SELL)

    },
    methods: {
      _getMultiData() {
        getMultiData().then(res => {
          this.banners = res.data.banner.list
          this.recommends = res.data.recommend.list
        })
      },
      _getProductData(type) {
        // 获取页码
        const page = this.goods[type].page
        console.log(page,type);
        getProductData(type, page).then(res => {
          console.log(res.data,"+++++++");
          console.log("---------");

          const newList = res.data.list
          this.goods[type].list.push(...newList)
          this.goods[type].page += 1

          // 完成加载更多数据
          this.$refs.scroll.finishedPullUp()
        })
      },
      tabClick(index) {
        switch (index) {
          case 0:
            this.currentType = POP
            break
          case 1:
            this.currentType = NEW
            break
          case 2:
            this.currentType = SELL
            break
        }

        this.$refs.contentTab.currentIndex = index
      },

    }
  }
</script>

<style scoped>
  #home {
    padding-top: 44px;
    height: 100vh;
  }

  .home-nav {
    background-color: var(--color-tint);
    color: #fff;
    position: fixed;
    left: 0;
    right: 0;
    top:0;
    z-index: 8;
  }

  .tab-control {
    position: sticky;
    top:44px;
    z-index: 9;
  }

  .content {
    position: absolute;
    top: 44px;
    bottom: 49px;
    left: 0;
    right: 0;
  }
</style>
