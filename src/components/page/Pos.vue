<template>
    <div class="pos">
        <el-row>
            <el-col :span='7' class="pos-order" id="order-list">
                <el-tabs>
                    <el-tab-pane label="点餐">
                        
                    </el-tab-pane>

                    <el-tab-pane label="挂单">
                        挂单
                    </el-tab-pane>

                    <el-tab-pane label="外卖">
                        外卖
                    </el-tab-pane>

                </el-tabs>
            </el-col>
            <el-col>
                我是产品栏
            </el-col>
        </el-row>
    </div>
</template>

<script>
export default {
    name:'pos',
    data () {
        return {
            tableData:[
                {goodsName:'可口可乐',price:8,count:1},
                {goodsName:'香辣鸡腿堡',price:15,count:1},
                {goodsName:'爱心薯条',price:8,count:1},
                {goodsName:'甜筒',price:8,count:1}
            ]
        }
    },
    mounted:function() {
        var orderHeight =document.body.clientHeight;
        document.getElementById("order-list").style.height=orderHeight +"px";
    }
}
</script>
<style scoped>
.pos-order {
    background-color: #f9fafc;
    border-right: 1px solid #c0ccda;
}

</style>
