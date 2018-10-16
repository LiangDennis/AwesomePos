<template>
    <div class="pos">
        <el-row>
            <el-col :span='7' class="pos-order" id="order-list">
                <el-tabs class="tabs">
                    <el-tab-pane label="点餐">
                        <el-table :data="tableData" border style="width:100%">
                            <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                            <el-table-column prop="count" label="量" width="40"></el-table-column>
                            <el-table-column prop="price" label="金额" width="50"></el-table-column>
                            <el-table-column label="操作" width="90" fixed="right">
                                
                                <template slot-scope="scope">
                                    <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                                    <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                                </template>
                            </el-table-column>
                        </el-table>
                        <div class="totalDiv">
                            <small>数量：</small>{{totalcount}} &nbsp;&nbsp;&nbsp;&nbsp; <small>金额：</small>{{totalMoney}}
                        </div>
                        <div class="div-btn">
                            <el-button type="warning" size="small">挂单</el-button>
                            <el-button type="danger" size="small" @click="delAllGoods()">删除</el-button>
                            <el-button type="success" size="small" @click="checkout()">结账</el-button>
                        </div>
                    </el-tab-pane>

                    <el-tab-pane label="挂单">
                        
                    </el-tab-pane>

                    <el-tab-pane label="外卖">
                        外卖
                    </el-tab-pane>

                </el-tabs>
            </el-col>
            <el-col :span="17">
                <div class="often-goods">
                    <div class="title">常用商品</div>
                    <div class="often-goods-list">
                        <ul>
                            <li 
                                v-for="(goods,index) in oftenGoods"
                                :key="index"
                                @click="addOrderList(goods)"
                            >
                                <span>{{goods.goodsName}}</span>
                                <span class="o-price">{{goods.price}}</span>
                            </li>
                        </ul>
                    </div>
                </div>

                <div class="goods-type">
                    <el-tabs>
                        <el-tab-pane label="汉堡">
                            <div>
                                <ul class="cookList">
                                    <li v-for="(goods,index) in type0Goods" :key="index" @click="addOrderList(goods)">
                                        <span class="foodImg"><img :src="goods.goodsImg" alt="" width="100%"></span>
                                        <span class="foodName">{{goods.goodsName}}</span>
                                        <span class="foodPrice">￥{{goods.price}}元</span>
                                    </li>
                                </ul>
                            </div>
                        </el-tab-pane>
                        <el-tab-pane label="小食">
                            <ul class="cookList">
                                    <li v-for="(goods,index) in type1Goods" :key="index" @click="addOrderList(goods)">
                                        <span class="foodImg"><img :src="goods.goodsImg" alt="" width="100%"></span>
                                        <span class="foodName">{{goods.goodsName}}</span>
                                        <span class="foodPrice">￥{{goods.price}}元</span>
                                    </li>
                                </ul>
                        </el-tab-pane>
                        <el-tab-pane label="饮料">
                            <ul class="cookList">
                                    <li v-for="(goods,index) in type2Goods" :key="index" @click="addOrderList(goods)">
                                        <span class="foodImg"><img :src="goods.goodsImg" alt="" width="100%"></span>
                                        <span class="foodName">{{goods.goodsName}}</span>
                                        <span class="foodPrice">￥{{goods.price}}元</span>
                                    </li>
                                </ul>
                        </el-tab-pane>
                        <el-tab-pane label="套餐">
                            <ul class="cookList">
                                    <li v-for="(goods,index) in type3Goods" :key="index" @click="addOrderList(goods)">
                                        <span class="foodImg"><img :src="goods.goodsImg" alt="" width="100%"></span>
                                        <span class="foodName">{{goods.goodsName}}</span>
                                        <span class="foodPrice">￥{{goods.price}}元</span>
                                    </li>
                                </ul>
                        </el-tab-pane>
                    </el-tabs>
                </div>

            </el-col>
        </el-row>
    </div>
</template>

<script>
import axios from "axios";
export default {
    name:'pos',
    data () {
        return {
            tableData:[],
            oftenGoods:[],
            type0Goods:[],
            type1Goods:[],
            type2Goods:[],
            type3Goods:[],
            totalMoney:0,
            totalcount:0
        }
    },
    created:function () {
        //常用商品
        axios.get('../static/oftenGoods.php')
        .then(
            response => {
                // console.log(response);
                this.oftenGoods =response.data;
            }
        )
        .catch(
            error => {
                // console.log(error);
                alert("网络错误，不能访问");
            }
        );
        //每种类型的商品
        axios.get('../static/typeGoods.php')
        .then(
            response => {
                // console.log(response);
                this.type0Goods =response.data[0];
                this.type1Goods =response.data[1];
                this.type2Goods =response.data[2];
                this.type3Goods =response.data[3];
            }
        )
        .catch(
            error => {
                alert("网络错误，不能访问");
            }
        );
    },
    mounted:function() {
        var orderHeight =document.body.clientHeight;
        document.getElementById("order-list").style.height=orderHeight +"px";
    },
    methods: {
        addOrderList(goods) {
            //每次添加时清零
            this.totalcount =0;
            this.totalMoney =0;

            //商品是否已经存在于订单列表中
            let isHave =false;
            for(let i=0;i<this.tableData.length;i++) {
                if (this.tableData[i].goodsId == goods.goodsId) {
                    isHave =true;
                }
            } 
            //根据判断的值编写业务逻辑
            if(isHave) {
                //改变列表中的商品数量
                // arr 只是一个过渡变量，filter 会过滤到id 相同的
                let arr =this.tableData.filter(o=>o.goodsId == goods.goodsId);
                arr[0].count++;
            }else {
                let newGoods ={
                    goodsId:goods.goodsId,
                    goodsName:goods.goodsName,
                    price:goods.price,
                    count:1
                };
                this.tableData.push(newGoods);
            }
            this.getAllMoney();
        },
        //删除单个商品
        delSingleGoods(goods) {
            this.tableData =this.tableData.filter(o=>o.goodsId !=goods.goodsId);
            this.getAllMoney();
        },
        //删除所有商品
        delAllGoods() {
            this.tableData =[];
            //因为计算时有更新
            this.totalMoney =0;
            this.totalcount =0;
        },
        //模拟结账
        checkout() {
            // 有数据才能结账，否则不允许结账
            if(this.totalcount!=0) {
                this.tableData =[];
                this.totalMoney =0;
                this.totalcount =0;
                this.$message.success("结账成功，谢谢惠顾");
                // this.$message({
                //     message:"结账成功，感谢惠顾",
                //     type:"success"
                // });
            }else {
                this.$message.error("没有商品，不能结账");
            }
        },
        //删除后从新计算
        getAllMoney () {
            this.totalMoney =0;
            this.totalcount =0;
            //有数据才汇总
            if (this.tableData) {
                // 计算与汇总数量和总价
                this.tableData.forEach((element)=> {
                    this.totalcount +=element.count;
                    this.totalMoney =this.totalMoney+(element.price*element.count);
                });
            }
        }
    },
}
</script>
<style scoped>
.pos-order {
    background-color: #f9fafc;
    border-right: 1px solid #c0ccda;
}
.div-btn {
    margin-top: 10px;
}
.tabs {
    padding-left: 10px;
    padding-right: 10px;
}
.title {
    height: 12px;
    border-bottom: 1px solid #d3dce6;
    background-color: #f9fafc;
    padding: 10px;
    text-align: left;
}
.often-goods-list ul li {
    list-style: none;
    float: left;
    border: 1px solid #e5e9f2;
    padding: 10px;
    margin: 10px;
    background-color: #fff;
    cursor: pointer;
}
.o-price {
    color: #58b7ff;
}
.goods-type {
    clear: both;
    margin: 10px;
}
.cookList li{
       list-style: none;
       width:25%;
       border:1px solid #E5E9F2;
       height: auto;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 2px;
       cursor: pointer;
   }
   .cookList li span{
        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodName{
       width: 50%;
       text-align: left;
       font-size: 16px;
       padding-left: 10px;
       color:brown;
   }
   .foodPrice{
       width: 40%;
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
   .totalDiv {
       background-color: #fff;
       padding: 10px;
       border-bottom: 1px solid #d3dce6;
   }
</style>
