<template>
	<div class="integralExchange" :style="{'padding-top':$store.state.paddingTop,height: 'calc(100% - '+ (parseInt($store.state.paddingTop.replace('px',''))+'px)')}">
		<!-- :style="{'padding-top':(parseInt($store.state.paddingTop.replace('px',''))-0)+'px'}" -->
			<div class="topNav" :style="{'padding-top':(parseInt($store.state.paddingTop.replace('px',''))-0)+'px','height':(parseInt($store.state.paddingTop.replace('px',''))+200)+'px','background-size':'100%'+' '+(parseInt($store.state.paddingTop.replace('px',''))+200)+'px'}">
				<div class="leftImg" @click="goBackFn"  id="navback">
					<img src="../../../assets/image/shape@2x.png" alt="">
				</div>
				<div class="centerTitle">
					<h3>积分兑换</h3>
				</div>
				<div class="integralExchangeNum">
					<h2>{{integral}}</h2>
				</div>
				<div class="integralExchangeButton">
					<router-link :to="{path : '/outpatient/outpatient_integralDetails',query:{time:new Date().getTime().toString()}}">
					<button>积分明细</button>
					</router-link>
					<router-link :to="{path : '/outpatient/outpatient_integralHistory',query:{time:new Date().getTime().toString()}}">
					<button>兑换记录</button>
					</router-link>
				</div>
			</div>
			<!-- :style="{'top':(parseInt($store.state.paddingTop.replace('px',''))+176)+'px'}" -->
			<div class="flowHeading" id ="flowHeading" :style="{'top':(parseInt($store.state.paddingTop.replace('px',''))+parseInt($store.state.paddingTop.replace('px',''))+176)+'px'}">
				<ul class="rollScreen_list" :style = {transform:transform}  :class="{rollScreen_list_unanim:num===0}">
					<!-- <li class="rollScreen_once" v-for="(item,index) in contentArr" :key='index'>
						<img src="../../../assets/image/horn@2x.png" alt="">
						<span>{{item}}</span>
					</li> -->
					<li class="rollScreen_once" v-for="(item,index) in contentArr" :key='index+contentArr.length' >
						<img src="../../../assets/image/horn@2x.png" alt="">
						<span>{{item}}</span>
					</li>
				</ul>
			</div>
			<div :style="{'height':(parseInt($store.state.paddingTop.replace('px',''))+228)+'px'}"></div>
			<integralExchangeList></integralExchangeList>
		
	</div>
</template>

<script>
import qs from 'qs';
import integralExchangeList from '../function/integralExchangeList.vue'
export default {
	name: 'integralExchange',
	data () {
		return {
			integral: 0,
			// 消息条数
			contentArr : [],
			//让消息显示为第一章的值
			num: 0,
			// 消息的计时器
			flowHeading : undefined,
			
		}
	},
	computed:{
		transform: function () {
	        return 'translateY(-' + this.num * .42 + 'rem)'
	    },
	},
	components:{
		integralExchangeList
	},
	destroyed() {

	},
	created () {
		let _this = this
		_this.flowHeading = setInterval(function () {
			if (_this.num !== _this.contentArr.length) {
				_this.num++
			} else {
				_this.num = 0
			}
		}, 2500)
	},
	destroyed() {
		window.clearInterval(this.flowHeading)
	},
	mounted() {
	},
	activated(){
		if(this.query != JSON.stringify(this.$route.query)){
			this.initData();
			this.query = JSON.stringify(this.$route.query);
			if(window.plus){
				//plus.navigator.setStatusBarBackground("#ffffff");
				plus.navigator.setStatusBarStyle("dark")
			}
		}
  	},
	methods: {
		goBackFn(){
			this.$router.back(-1);
		},
		getData(){
			// 从接口获取消息
			this.$axios.post('/clientend2/clinicend/pointexchange/msgs',qs.stringify({
				clinicId : this.$store.state.outpatient.login.clinicId,
				pn : 1,
				ps : 10
			}))
			.then(res => {
				if(res.data.codeMsg == '' || res.data.codeMsg == null || res.data.codeMsg == undefined){
					for(let i in res.data.data.items){
						if(res.data.data.items[i].title){
							this.contentArr.push(res.data.data.items[i].title);
						}
					}
				}else{
					this.$toast(res.data.codeMsg)
				}
			})
			.catch((err)=>{
				this.$toast(err)
			})
			this.$axios.post('/clientend2/clinicend/pointexchange/main',qs.stringify({
				clinicId : this.$store.state.outpatient.login.clinicId,
				pn : 1,
				ps : 10
			}))
			.then(res => {
				this.integral = res.data.data.exchangePoint
			})
			.catch((err)=>{
				this.$toast(err)
			})
		},
		initData() {
			Object.assign(this.$data, this.$options.data());
			this.getData();
		},
		
	},
}
</script>

<style scoped>
.integralExchange{
	width:100%;
	height: 100%;
	overflow: hidden;
	background-color: #F5F5F5;
}
.topNav{
	width: 100%;
	height: 2.1rem;
	background: url('../../../assets/image/blue-BJ@2x.png')  top no-repeat,linear-gradient(#FDFDFD, #FBFBFB) ;
	background-size: 100% 2.1rem;
	color: #FFFFFF;
	text-align: center;
	position: fixed;
	top:0;
	z-index: 99;
	box-sizing: border-box
}
.zhangwei{
	width: 100%;
	height: 1.76rem;
}
.leftImg{
	width: 22%;
	height: .47rem;
	line-height: .47rem;
	float:left;
	text-align: left;
}
.leftImg img{
	width: .09rem;
	height: .15rem;
	line-height: .47rem;
	padding-left: .16rem;
}
.centerTitle{
	width: 56%;
	text-align: center;
	height: .47rem;
	line-height: .47rem;
	float:left;
	/* display:inline-block; */
}
.centerTitle h3{
	font-size: .16rem;
	font-weight: bolder;
}
.right{
	width: 22%;
	height: .47rem;
	line-height: .47rem;
	text-align: right;
	float:left;
}
.integralExchangeNum{
	display: block;
	float: left;
	width: 100%;
	height: .54rem;
	line-height: .54rem;
	text-align: center;
}
.integralExchangeNum h2{
	display: block;
	font-size: .36rem;
}
.integralExchangeButton{
	width: 100%;
	float: left;
	margin-top: .2rem;
}
.integralExchangeButton a button{
	width: 1.1rem;
	height: .29rem;
  color: #FFFFFF;
	background: none;
	border: 1px solid #FFFFFF;
	border-radius: .15rem;
}
.integralExchangeButton a:first-child button{
	margin-right: .5rem;
}
.flowHeading{
	width: 100%;
	height: .42rem;
	line-height: .42rem;
	background-color: #FFFFFF;
	display: inline-block;
	overflow: hidden;
	/* position:relative; */
	position: fixed;
	z-index: 9999;
}
.flowHeading ul li{
	width: 100%;
	height: .42rem;
	line-height: .42rem;
	font-size: .15rem;
}
.flowHeading ul li img{
	width: .17rem;
	height: .16rem;
	margin-left: .16rem;
	margin-right: .075rem;
	margin-top: -.03rem;
}
.rollScreen_list{
	transition: 1s linear;
}
.rollScreen_list_unanim{
	transition: none
}
.productsExchange{
	/* height: calc(100% - 2.28rem); */
}
</style>
