<template>
<div>
	<el-row id='elRow'>
		<el-col :span='8' id='order-list'>
			<el-tabs type='border-card' id='elTabs'>
				<el-tab-pane>
					<span slot="label"><i class="icon iconfont icon-diancan"></i> <b style='margin-top: -3px;'>点餐</b></span>
					<el-table :data='tableData' style='width:100%;' border>
						<el-table-column prop='goodsName' label='商品'></el-table-column>
						<el-table-column prop='count' label='数量'></el-table-column>
						<el-table-column prop='price' label='金额'></el-table-column>
						<el-table-column label='操作' fixed='right'>
							<template slot-scope='scope'>
								<el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
            					<el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
							</template>
						</el-table-column>
					</el-table>
					<div class='div-total'>
						<small>总数量：</small><b>{{totalCount}}</b>
						<small>总金额：</small><b>￥{{totalMoney}}</b>
					</div>
					<div class='div-btn'>
						<el-button type="warning" >挂单</el-button>
						<el-button type="danger" @click="delAllGoods">删除</el-button>
						<el-button type="success" @click='checkOutMoney'>结账</el-button>
					</div>
				</el-tab-pane>
				<el-tab-pane>
					<span slot="label"><i class="icon iconfont icon-guadanshouyin"></i> <b style='margin-top: -3px;'>挂单</b></span>
					挂单
				</el-tab-pane>
				<el-tab-pane>
					<span slot="label"><i class="icon iconfont icon-waimai"></i> <b style='margin-top: -3px;'>外卖</b></span>
					外卖
				</el-tab-pane>
				
			</el-tabs>
		</el-col>
		<el-col :span="16" id='prod-list'>
			<div class='often-goods'>
				<div class='title'>这是产品栏</div>
				<div class="often-goods-list">
					<ul>
						<li v-for='(goods,index) in oftenGoods' @click="addOrderList(goods)">
							<span>{{goods.goodsName}}</span>
							<span class='o-price'>￥{{goods.price}}</span>
						</li>
					</ul>
				</div>
			</div>
			<div class="goods-type" id='goods'>
				<el-tabs type="border-card">
					<el-tab-pane>
						<span slot="label"><i class="icon iconfont icon-hanbao"></i> <b style='margin-top: -3px;'>汉堡</b></span>
						<ul class='cookList'>
						    <li v-for='(goods,index) in type0Goods' @click="addOrderList(goods)">
						        <span class="foodImg"><img :src="goods.goodsImg" width='100%'></span>
						        <span class="foodName">{{goods.goodsName}}</span>
						        <span class="foodPrice">￥{{goods.price}}</span>
						    </li>
						</ul>
					</el-tab-pane>
					<el-tab-pane>
						<span slot="label"><i class="icon iconfont icon-xiaoshi-xuanzhong"></i> <b style='margin-top: -3px;'>小食</b></span>
						<ul class='cookList'>
						    <li v-for='(goods,index) in type1Goods' @click="addOrderList(goods)">
						        <span class="foodImg"><img :src="goods.goodsImg" width='100%'></span>
						        <span class="foodName">{{goods.goodsName}}</span>
						        <span class="foodPrice">￥{{goods.price}}</span>
						    </li>
						</ul>
					</el-tab-pane>
					<el-tab-pane>
						<span slot="label"><i class="icon iconfont icon-yinliao1"></i> <b style='margin-top: -3px;'>饮料</b></span>
						<ul class='cookList'>
						    <li v-for='(goods,index) in type2Goods' @click="addOrderList(goods)">
						        <span class="foodImg"><img :src="goods.goodsImg" width='100%'></span>
						        <span class="foodName">{{goods.goodsName}}</span>
						        <span class="foodPrice">￥{{goods.price}}</span>
						    </li>
						</ul>
					</el-tab-pane>
					<el-tab-pane>
						<span slot="label"><i class="icon iconfont icon-taocan"></i> <b style='margin-top: -3px;'>套餐</b></span>
						<ul class='cookList'>
						    <li v-for='(goods,index) in type3Goods' @click='addOrderList(goods)'>
						        <span class="foodImg"><img :src="goods.goodsImg" width='100%'></span>
						        <span class="foodName">{{goods.goodsName}}</span>
						        <span class="foodPrice">￥{{goods.price}}</span>
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
	import axios from 'axios'
	export default {
		name:'Pos',
		data(){
			return {
			  tableData: [],
		      oftenGoods:[],
		      type0Goods:[],
		      type1Goods:[],
		      type2Goods:[],
		      type3Goods:[],
		      totalMoney:0,
		      totalCount:0,

			}
		},
		methods:{
			addOrderList(goods){
				
				//1首先判断tableData是否有数据
				//console.log(goods);
				let isHave = false;
				for( let i=0;i<this.tableData.length;i++ ){
					if(this.tableData[i].goodsId==goods.goodsId){
						isHave = true;
					}
				}

				//数据存在与不存在时进行
				if(isHave){//存在
					console.log(this.tableData)
					let arr = this.tableData.filter(o=>o.goodsId===goods.goodsId);//获取相同的对象
					arr[0].count++;
					//console.log(arr);//应该是一个数组包含着对象
				}else{//不存在
					let newData = {goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
					this.tableData.push(newData);
				}
				this.getAllMoney();
			},

			//单商品删除
			delSingleGoods(goods){
				/*this.tableData = this.tableData.filter(o=>o.goodsId!==goods.goodsId);*/
				//console.log(this.tableData)
				
				this.tableData.forEach((o,i)=>{
					//console.log(o,i)
					//console.log(this.tableData)
					if(o.goodsId===goods.goodsId){
						o.count&&o.count--;
						if(o.count===0){
							this.tableData.splice(i,1);//删除一位
							console.log(this.tableData);
						}
					}
					return  o;
				})
				this.getAllMoney();
				
				
			},


			//删除全部商品
			delAllGoods(){
				if(this.tableData.length){
					this.tableData = [],
					this.totalMoney = 0;
					this.totalCount = 0;
				}
			},


			//模拟后台结账
			checkOutMoney(){
				if(this.totalMoney&&this.totalCount){//当有数据时才进行结账处理
					//结账之后要把总金额和总数量清空
					this.tableData = [];
					this.totalMoney = 0;
					this.totalCount = 0;
					this.$message({
						message:'恭喜你，已经结账成功!',
						type:'success',
					});
				}else{
					this.$message.error('不能空结账哦，老板知道你急切的心情!');
				}
			},
			getAllMoney(){
				//
				this.totalMoney = 0;
				this.totalCount = 0;
				if(this.tableData.length){
					//计算数量和总金额
					//
					this.tableData.forEach(ele=>{
						this.totalCount += ele.count;
						this.totalMoney += (ele.count*ele.price);
					})
				}
			},

		},
		mounted:function(){
			getBodyHeight('order-list');
			getBodyHeight('prod-list');
			// getBodyHeight('elTabs');
		},
		created:function(){
			axios.get('http://jspang.com/DemoApi/oftenGoods.php')
				 .then(response=>{
				 	this.oftenGoods = response.data;
				 	//console.log(response);
				 })
				 .catch(error=>{
				 	alert('服务器出错'+ error);
				 });
		    axios.get('http://jspang.com/DemoApi/typeGoods.php')
		         .then(response=>{
		         	this.type0Goods = response.data[0];
		         	this.type1Goods = response.data[1];
		         	this.type2Goods = response.data[2];
		         	this.type3Goods = response.data[3];
		         	//console.log(response);
		         })
		},
	}
	function getBodyHeight(str){
		var orderListHeight = document.body.clientHeight;
		console.log(orderListHeight);
		document.getElementById(str).style.height=orderListHeight+'px';
	}
</script>

<style scoped>
#elRow{
	height: 100%;
	padding-top:0;
	font-size:20px;
}
.div-btn{
	margin-top: 30px;
}
.title{
   height: 20px;
   border-bottom:1px solid #D3DCE6;
   background-color: #F9FAFC;
   padding:9px;
}
.often-goods-list ul li{
  list-style: none;
  float:left;
  border:1px solid #E5E9F2;
  padding:10px;
  margin:5px;
  background-color:#fff;
  cursor: pointer;
}
.o-price{
  color:#58B7FF; 
}
.goods-type{
	clear: both;
	height: auto;
	margin-top: 20px;
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
   font-size: 16px;
   padding-left: 10px;
   color:brown;

}
.foodPrice{
   font-size: 16px;
   padding-left: 10px;
   padding-top:10px;
}
.div-total{
	background-color: #fff;
	padding-top: 5px;
	height: 30px;
	border-bottom: 1px solid pink;
}
.div-total small{
	font-size: 12px;
	font-family: 微软雅黑;
}
.div-total b{
	color: red;
}
</style>