<template>
  <div id="home">
    <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
    <scroll :probe-type="3"
            :pull-up-load="true"
            class="content"
            @scroll="contentScroll"
            @pullingUp="loadMore"
            :data="[showGoodsList]"
            ref="scroll">
        <swiper :banners="banners"/>
        <recommend-menu :recommends="recommends"/>
        <hot/>
        <tab-control ref="contentTab" class="tab-control" @tabClick="tabClick"></tab-control>
        <goods-list :goods="showGoodsList"></goods-list>
    
    </scroll>
    <back-top @click.native="backClick" v-show="isShowBackTop"/>
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
  import BackTop from 'components/content/backTop/BackTop'

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
      BackTop,
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
        isShowBackTop: false,

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
          const newList = res.data.list
          this.goods[type].list.push(...newList)
          this.goods[type].page += 1

          // 完成加载更多数据
          this.$refs.scroll.finishPullUp()
          this.$refs.scroll.refresh()

        })
      },
      loadMore() {
        this._getProductData(this.currentType)
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
      backClick() {
        this.$refs.scroll.scrollTo(0, 0)
      },
      contentScroll(position) {
        this.isShowBackTop = (-position.y) > 1000
      },


    }
  }
</script>

<style scoped>
  #home {
    /* padding-top: 44px; */
    position: relative;
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
    z-index: 9;
  }

  .content {
    height: calc(100% - 93px);
    overflow: hidden;
    margin-top: 44px;
  }
</style>
