<!--  -->
<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav" 
                    @titleClick="titleClick"
                    ref="nav">
                    bot</detail-nav-bar>
                    lkjaslfkjsla;jf
    </div>
</template>

<script>
//这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
//例如：import 《组件名称》 from '《组件路径》';
import DetailNavBar from './childComps/DetailNavBar';
import DetailSwiper from './childComps/DetailSwiper';
import DetailBaseInfo from './childComps/DetailBaseInfo';
import DetailShopInfo from './childComps/DetailShopInfo';
import DetailGoodsInfo from './childComps/DetailGoodsInfo';
import DetailParamInfo from './childComps/DetailParamInfo';
import DetailCommentInfo from './childComps/DetailCommentInfo';
import DetailBottomBar from './childComps/DetailBottomBar';

import Scroll from 'components/common/scroll/Scroll';
import GoodsList from 'components/content/goods/GoodsList';

import {getDetail, getRecommend, Goods, Shop, GoodsParam} from 'network/detail';
import {debounce} from "common/utils";
// import {itemListenerMixin, backTopMixin} from "common/mixin";
import {BACK_POSITION} from "common/const";

export default {
//import引入的组件需要注入到对象中才能使用
name:"Detail",
components: {
    DetailNavBar,
},
data() {
//这里存放数据
return {
iid:null,
data:[],
topImages: [],
goods: {},
shop:{},
detailInfo: {},
paramInfo: {},
commentInfo: {},
recommends: [],
themeTopYs: [0, 1000, 2000, 3000],
getThemeTopY: null,
currentIndex: 0,
};
},
//监听属性 类似于data概念
computed: {},
//监控data中的数据变化
watch: {},
//方法集合
methods: {
titleClick(index){
    
}
},
//生命周期 - 创建完成（可以访问当前this实例）
created() {
    this.currentIndex++;
    console.log(this.currentIndex);
this.iid = this.$route.params.iid;
    console.log(this.iid);
    console.log(this);

getDetail(this.iid).then(res => {
    const data = res.result;
            this.topImages = res.result.itemInfo.topImages;

            //获取商品信息
            this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services);
            
            //获取店铺信息
            this.shop = new Shop(data.shopInfo);
            //console.log(this.shop);

            //获取商品详细信息
            this.detailInfo = data.detailInfo;
            //console.log(this.detailInfo);

            //获取店铺商品信息
            this.paramInfo = new GoodsParam(data.itemParams.info, data.itemParams.rule);

            //获取评论信息
            if(data.rate.cRate !== 0){
                this.commentInfo = data.rate.list[0];
            }
})
      //获取推荐信息
      getRecommend().then(res => {
        //  console.log(res);
        this.recommends = res.data.list;
      })

},

//生命周期 - 挂载完成（可以访问DOM元素）
mounted() {

},
beforeCreate() {}, //生命周期 - 创建之前
beforeMount() {}, //生命周期 - 挂载之前
beforeUpdate() {}, //生命周期 - 更新之前
updated() {}, //生命周期 - 更新之后
beforeDestroy() {}, //生命周期 - 销毁之前
destroyed() {}, //生命周期 - 销毁完成
activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
}
</script>
<style scoped>
/* @import url(); 引入公共css类 */

</style>
