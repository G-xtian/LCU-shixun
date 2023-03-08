<template>
  <div class="goodsDetails">
      <el-card class="box-card">
          <div slot="header" class="clearfix">
              <h2>商品信息详情</h2>
              <el-button style="float: right;padding: 3px 0" type="text" @click="back()">返回</el-button>
          </div>
          <div class="text item">
              <el-row>
                    <el-col :span="6">
                        <carousel :src="path + goodsDetails.savepath" :pics="imagesList"></carousel>
                    </el-col>
                    <el-col :span="14">
                        <div class="goodsTitle">{{goodsDetails.name}}</div>
                        <div class="goodsInfo"><i class="el-icon-goods"></i>库存:{{goodsDetails.stock}}</div>
                        <div class="goodsInfo"><i class="el-icon-shopping-cart-2"></i>价格:{{goodsDetails.price}}</div>
                        <div class="introduce">{{goodsDetails.introduce}}</div>
                        <el-input-number class="numberInput" v-model="num"  :min="1" :max="100" label="描述文字"></el-input-number><br>
                        <el-button type="warning" plain>加入购物车</el-button>
                        <el-button type="danger" plain>立即购买</el-button>
                    </el-col>
              </el-row>

          </div>
      </el-card>
  </div>
</template>

<script>
// 引入鼠标悬停仿京东预览商品详情
import Carousel from '@/components/carousel.vue'
export default {
    // 引入鼠标悬停仿京东预览商品详情
    components: {
        Carousel
    },
    data(){
        return{
            num: 1,
            imagesList:[],
            goodsDetails:{}
        }
    },
    methods:{
        // 返回上一级路由函数
        back(){
            this.$router.go(-1)
        }

    },
    created(){
        // 将列表页面传输过来的JSON类型的js字符串转化为js对象
        this.goodsDetails = JSON.parse(this.$route.query.str)
        let pics = []
        this.goodsDetails.goodsPicList.forEach((pic,index) => {
            pics.push(this.path + pic.savepath)
        });
        this.imagesList = pics
    }

}
</script>

<style scoped>
    @import url('GoodsDetails.css');

    .el-button--text{
        margin-top: -20px;
        font-size: 18px;
        font-weight: 600;
    }
</style>