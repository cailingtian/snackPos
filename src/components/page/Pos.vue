<template>
  <div class="pos">
    <el-row>
        <el-col :span = '7' class="pos-order" id="pos-list">
          <el-tabs class="tabs">
            <el-tab-pane label="点餐" >
              <!-- 表格 -->
             <el-table boeder :data="tableData" style="width:100%">
              <el-table-column prop="goodsName" label="商品名称" width="100"></el-table-column>
              <el-table-column prop="count" label="数量" width="50"></el-table-column>
              <el-table-column prop="price" label="金额" width="50"></el-table-column>
              <el-table-column label="操作" width="110.72" fixed="right">
                <template slot-scope="scope">
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                  <el-button type="text" size="small" @click="deleteSingleGoods(scope.row)">删除</el-button>
                </template>
              </el-table-column>
             </el-table>

             <div class="totalDiv">
                <small>数量：</small>{{totalCount}} &nbsp;&nbsp;&nbsp;&nbsp;
                  <small>总计：</small>{{totalMoney}}元
                 </div>
             <!-- 三个按钮 -->
             <div class="div-btn">
               <el-button type="warning">挂单</el-button>
               <el-button type="danger" @click="deleteAllGoods">删除</el-button>
               <el-button type="success" @click=" checkout">结账</el-button>
             </div>
            </el-tab-pane>
            <el-tab-pane label=" 挂单">
              挂单
            </el-tab-pane>
            <el-tab-pane label="外卖" >
              外卖
            </el-tab-pane>
          </el-tabs>

        </el-col>
        <el-col :span="17">
          <!-- 热销商品 -->
          <div class="often-goods">
            <div class="title">热销商品</div>
            <div class="hot-goods">
              <ul>
                <li v-for="goods in hotGoods" :key="goods.goodsId" @click="addOrderList(goods)">
                  <span>{{goods.goodsName}}</span>
                  <span class="h-price">￥{{goods.price}}元</span>
                </li>     
              </ul>
            </div>
          </div>

          <!-- 点餐商品 -->
        <div class="goods-type">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <div>
               <ul class='cookList'>
                  <li v-for="goods in type0Goods" :key="goods.goodsId" @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
              </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="小食">
               <div>
                <ul class='cookList'>
                    <li v-for="goods in type2Goods" :key="goods.goodsId" @click="addOrderList(goods)">
                        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                        <span class="foodName">{{goods.goodsName}}</span>
                        <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="饮料">
               <div>
                <ul class='cookList'>
                    <li v-for="goods in type2Goods" :key="goods.goodsId" @click="addOrderList(goods)">
                        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                        <span class="foodName">{{goods.goodsName}}</span>
                        <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="套餐">
               <div>
                <ul class='cookList'>
                    <li v-for="goods in type3Goods" :key="goods.goodsId" @click="addOrderList(goods)">
                        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                        <span class="foodName">{{goods.goodsName}}</span>
                        <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                </ul>
              </div>
            </el-tab-pane>
          </el-tabs>
        </div>
        </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'Pos',
  data () {
    return {
      tableData: [],
      hotGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totalCount: 0,
      tatalMoney: 0
    }
  },
  created: function() {
    axios.get('http://jspang.com/DemoApi/oftenGoods.php')
    .then(response=>{
      //console.log(response);
      this.hotGoods = response.data;
    })
    .catch(error=>{
      //console.log(error);
      alert("网络出现异常，请稍后再试");
    })

    axios.get('http://jspang.com/DemoApi/typeGoods.php')
    .then(response=>{
       //console.log(response);
      this.type0Goods = response.data[0];
      this.type1Goods = response.data[1];
      this.type2Goods = response.data[2];
      this.type3Goods = response.data[3];
    })
    .catch(error=>{
      alert("网络出现异常，请稍后再试");
    })
  },
  mounted: function() {
    var orderHeight = document.body.clientHeight;
    //console.log(orderHeight);
    document.getElementById('pos-list').style.height = orderHeight + "px";
  },
  methods: {
    addOrderList (goods) {
      //console.log(goods);
      //商品是否存在订单列表中
      let isHave = false;
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId == goods.goodsId){
            isHave = true;
        } 
      }
      //根据判断的值写业务逻辑
      if (isHave) {
        //改变列表中商品的数量
        let arr = this.tableData.filter(a => a.goodsId == goods.goodsId);
        arr[0].count++;
      } else {
        let newGoods = {goodsId:goods.goodsId, goodsName:goods.goodsName, price:goods.price, count:1}
        this.tableData.push(newGoods);
      }
     //调用计算总金额和数量方法
      this.getAllData();
    },

    //删除单个商品
    deleteSingleGoods (goods) {
      this.tableData = this.tableData.filter(a => a.goodsId != goods.goodsId);
      //调用计算总金额和数量方法
      this.getAllData();
    },
    //删除所有商品
    deleteAllGoods () {
      this.tableData = [];
      this.totalCount = 0;
      this.totalMoney = 0;
    },

    //模拟结账
    checkout () {
      if (this.tableData != 0) {
        this.tableData = [];
        this.totalCount = 0;
        this.totalMoney = 0;
        this.$message({
          message: "结账成功,感谢您的光临！",
          type: "success"

        });
      } else {
        this.$message({
          message: "您还目前没有选择商品哦！",
          type: "error"
        });
      }
    },

    //汇总金额和数量
    getAllData () {
      //每次点击增加或删除都清零重新计算
      this.totalCount = 0;
      this.totalMoney = 0;
      if (this.tableData) {
        //计算总价和数量
        this.tableData.forEach((element) => {
          this.totalCount += element.count;
          this.totalMoney = this.totalMoney + (element.count * element.price);
        });
      }
    }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .pos-order {
    background-color:#F9FAFC;
    border-right: 1px solid #C0CCDA;
  }
  .div-btn {
    margin: 15px;
  }
  .title {
    height: 20px;
    border-bottom: 1px solid #D3dce6;
    background-color: #F9FAFC;
    padding: 10px;
    text-align: left;
  }
  .hot-goods ul li {
    list-style: none;
    float: left;
    border: 1px solid #E5E9F2;
    padding: 10px;
    margin: 10px;
    background-color: #fff;
    border-radius: 5%;
  }
  .h-price {
    color: #58B7FF;
  }
  .goods-type {
   clear: both;
  }
  .cookList li{
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auot;
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
       font-size: 18px;
       padding-left: 10px;
       color:brown;
 
   }
   .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
   .totalDiv {
     background-color: #fff;
     padding: 10px;
     border-bottom: 1px solid #D3dce6;

   }
</style>
