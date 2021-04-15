<!--  -->
<template>
<div class='recommend'>
    <div class="recommend-item" v-for="(item, index) in recommends" :key="index">
      <a :href="item.link">
        <img :src="item.image">
        <div display:block>{{item.title}}</div>
      </a>  
    </div>
</div>
</template>

<script>
//这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
//例如：import 《组件名称》 from '《组件路径》';

export default {
    props: {
        name: "RecommendMenu",
        recommends:{
          type:Array,
          required:true
        }
    }
}
</script>
<style scoped>
/* @import url(); 引入公共css类 */
  .recommend {
    display: flex;
    margin-top: 10px;
    font-size: 14px;
    padding-bottom: 10px;
    border-bottom: 10px solid #eee;
  }

  .recommend-item {
    flex: 1;
    text-align: center;
    align-content: center;
  }

  .recommend img {
    width: 60px;
    margin-bottom: 10px;
  }

</style>
