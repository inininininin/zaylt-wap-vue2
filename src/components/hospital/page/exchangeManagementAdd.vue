<template>
	<div class="exchangeAdd">
		<div class="topNav" :style="{'padding-top':$store.state.paddingTop}">
			<div class="leftImg" @click="goBackFn"  id="navback">
				<img src="../../../assets/image/shape@3x.png" alt="">
			</div>
			<div class="centerTitle">
				<h3>新增商品</h3>
			</div>
			<div class="right">
				<button @click="nextFn">下一步</button>
			</div>
		</div>
		<div class="zhangwei"></div>
		<ul :style="{'padding-top':$store.state.paddingTop}">
			<li>
				<span>商品名称</span>
				<p>
					<input type="text" v-model="exchangeAdd.name">
				</p>
			</li>
			<li>
				<span>单个积分</span>
				<p>
					<input type="number" onKeypress="return(/[\d\.]/.test(String.fromCharCode(event.keyCode)))" v-model="exchangeAdd.payExchangepoint"> 分
				</p>
			</li>
			<li>
				<span>总数量</span>
				<p>
					<input type="number"  onKeypress="return(/[\d\.]/.test(String.fromCharCode(event.keyCode)))"  v-model="exchangeAdd.stock"> 个
				</p>
			</li>
			<li>
				<span>简介</span>
				<span v-show="exchangeAdd.intro? false:true">请输入</span>
				<textarea v-model="exchangeAdd.intro"></textarea>				
			</li>
		</ul>
	</div>
</template>

<script>
import qs from 'qs';
export default {
	name: 'exchangeAdd',
	data () {
		return {
			// 医院端兑换管理的新增和商品修改信息参数
			exchangeAdd:{
				path : '/hospital/',
				payExchangepoint : '',
				stock : '',
				intro : '',
				cover : '',
				show : true,
			},
			query:''
		}
	},
	computed:{
	},
	components:{
		
	},
	created(){
	},
  	mounted() {
	},
	activated() {
		if(this.query != JSON.stringify(this.$route.query)){
			Object.assign(this.$data, this.$options.data());
			this.query = JSON.stringify(this.$route.query);
			if(window.plus){
				plus.navigator.setStatusBarStyle("dark")
			}
		}
	},
	methods: {
		//回退方法
		goBackFn(){
			this.exchangeAdd = {
				path : '/hospital/',
				payExchangepoint : 0,
				stock : 0,
				intro : '',
				cover : '',
				show : true,
			}
			this.$router.back();
		},
		nextFn(){
			if(this.exchangeAdd.name != ''){
				if(this.exchangeAdd.payExchangepoint != ''){
					if(this.exchangeAdd.stock != ''){
						if(this.exchangeAdd.intro != ''){
							this.$router.push({ path : '/hospital/hospital_exchangeManagementImg',query : {exchangeAdd : JSON.stringify(this.exchangeAdd),time: new Date().getTime().toString()}});
						}else{
							this.$toast.fail('请填写简介');
						}
					}else{
						this.$toast.fail('请填写数量');
					}
				}else{
					this.$toast.fail('请填写积分');
				}
			}else{
				this.$toast.fail('请填写名称');
			}
		},
	},
}
</script>

<style scoped>
.exchangeAdd{
	width: 100%;
}
.topNav{
	width: 100%;
	height: .47rem;
	margin-bottom: .15rem;
	background-color: #FFFFFF;
	border-bottom: 1px solid #D8D8D8;
	position: fixed;
	top:0;
	z-index: 9999;
}	
.zhangwei{
	width: 100%;
	height: .62rem;
}
.leftImg{
	width: 18%;
	height: .47rem;
	line-height: .47rem;
	float:left;
}
.leftImg img{
	width: .09rem;
	height: .15rem;
	line-height: .47rem;
	padding-left: .16rem;
}
.centerTitle{
	width: 64%;
	text-align: center;
	height: .47rem;
	line-height: .47rem;
	float:left;
}
.centerTitle h3{
	font-size: .16rem;
	font-weight: bolder;
}
.right{	
	width: 18%;
	height: .47rem;
	line-height: .47rem;
	float:right;
}
.right button{
	width: .56rem;
	height: .3rem;
	line-height: .3rem;
	text-align: center;
	color: #FFFFFF;
	background-color: #2B77EF;
	border: none;
	border-radius: .03rem;
	padding: 1px 3px;
}
.exchangeAdd ul{
	width: 100%;
}
.exchangeAdd ul li{
	width: 91.46%;
	height: .45rem;
	line-height: .45rem;
	margin: 0 auto;
	border: 1px solid #D8D8D8;
	margin-bottom: .12rem;
}
.exchangeAdd ul li:last-child{
	height: 3.65rem;
	line-height: normal;
	padding-top: .12rem;
}
.exchangeAdd ul li:last-child span:nth-child(2){
	color: #2B77EF;
}
.exchangeAdd ul li span{
	font-size: .14rem;
	margin-left: .15rem;
	color: #333333;
}
.exchangeAdd>ul>li>span:nth-child(2){
	float: right;
	margin-right: .15rem;
	color: #666666;
}
.exchangeAdd ul li p{
	float: right;
	margin-right: .15rem;
	font-size: .14rem;
}
.exchangeAdd ul li p>input{
	border: none;
	height: .38rem;
	text-align: right;
	color: #2B77EF;
}
.exchangeAdd ul li textarea{
	width: 91.25%;
	height: 3.3rem;
	margin: 0rem auto;
	line-height: .15rem;
	display: block;
	color: #2B77EF;
	font-size: .13rem;
	border: none;
	margin-top: .03rem;
}
</style>
