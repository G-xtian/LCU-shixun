<template>
    <div class="petGoodsList">
        <!-- 搜索框 -->
        <div class="queryClass">
            <el-input
                class="queryInput"
                placeholder="搜索商品"
                prefix-icon="el-icon-search"
                v-model="queryFrom.name">
            </el-input>
            <el-button class="queryButton" type="primary" @click="queryAllGoodsByName()">搜索</el-button>
        </div>
        <!-- 商品列表 -->
        <el-row :gutter="25" class="rowClass">
            <el-col :span="4.2" v-for="goods in goodsList" :key="goods.id">
                <div @mousemove="big(goods.id)" @mouseleave="small(goods.id)" >
                    <el-card :ref="goods.id" class="goodsCard" shadow="hover">
                        <div v-show="goods.stock === 0" class="iconfont icon-lianxi-yishouqing kong"></div>
                        <el-row>
                            <el-col :span="10">
                                <el-image class="goodsImage" :src="path + goods.savepath" @click="showGoodsDetails(goods)"></el-image>
                            </el-col>
                            <el-col class="goodsInfoCol" :span="14">
                                <span>{{path}}</span>
                                <div class="goodsTitle">{{goods.name}}</div>
                                <div class="goodsInfo"><i class="el-icon-goods"></i>库存:{{goods.stock}}</div>
                                <div class="goodsInfo"><i class="el-icon-shopping-cart-2"></i>价格:{{goods.price}}</div>
                                <div class="introduce">{{goods.introduce.length > 40 ?goods.introduce.substring(0,40) + '...':goods.introduce}}</div>
                            </el-col>
                        </el-row>
                    </el-card>
                </div>
            </el-col>
        </el-row>
        <!-- 分页条 -->
        <div class="paginationClass">
             <el-pagination
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="pageNum"
                :page-sizes="[5, 8, 11, 14]"
                :page-size="pageSize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="total">
            </el-pagination>
        </div>
    </div>
  
</template>

<script>
export default {
    data(){
        return{
            pageNum:1,
            pageSize:5,
            total:0,
            queryFrom:{
                name:''
            },
            goodsList:[]
        }
    },
    methods:{
        //商品详情页面
        showGoodsDetails(goods){
            let str = JSON.stringify(goods)
            this.$router.push({path:'/goodsDetails',query:{str}})
        },
        // 鼠标悬停上浮动画
         big(id){
            this.$refs[id][0].$el.style.transform= 'scale(1.1)'
        },
        small(id){
            this.$refs[id][0].$el.style.transform= 'scale(1)'
        },
        //当页面记录数发生改变时触发的函数
        handleSizeChange(pageSize){
            this.pageSize = pageSize
            this.queryAllGoodsList(this.$route.params.petType)
        },
        //当页码发生改变的时候触发的函数
        handleCurrentChange(pageNum){
            this.pageNum = pageNum
            this.queryAllGoodsList(this.$route.params.petType)
        },
        // 查询宠物商品
        queryAllGoodsByName(){
            this.queryAllGoodsList(this.$route.params.petType)
        },
        // 查询所有商品列表的函数
        queryAllGoodsList(petType){
            let param = (petType === 'cat'?2:1)
            this.$http.get('/goods/queryAllGoods',{params:{
                petType:param,
                name:this.queryFrom.name,
                pageNum:this.pageNum,
                pageSize:this.pageSize
            }}).then(res=>{
                if(res.data.code === 200){
                    this.goodsList = res.data.data.rows
                    this.pageNum = res.data.data.pageNum
                    this.total = res.data.data.total
                    console.log("地址",res.data.data.rows)
                }
            })
        }
    },
    created(){
        this.queryAllGoodsList(this.$route.params.petType)
    },
    //监听器 用来监听
    watch:{
        $route(to,from){
            if(to.path === '/petGoodsList/cat'){
                this.queryAllGoodsList('cat')
            }else if(to.path === '/petGoodsList/dog'){
                this.queryAllGoodsList('dog')
            }else{

            }
        }
    }
}
</script>

<style scoped>
    @import url('PetGoodsList.css');
</style>