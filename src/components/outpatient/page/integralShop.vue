<template>
	<div class="integralShop">
		<div class="slip">
			<div class="return" @click="returnFn" id="navback" :style="{'padding-top':$store.state.paddingTop}">
				<img src="../../../assets/image/shape@3x.png" alt="">
			</div>
			<van-swipe >
			  <van-swipe-item v-for="(image, index) in shopDetails.cover" :key="index" :height="375">
			    <img v-lazy="image" class="silder_img"/>
			  </van-swipe-item>
			</van-swipe>
		</div>
		<div class="shopAbout">
			<h4>{{shopDetails.name}}</h4>
			<p>{{shopDetails.intro}}</p>
		</div>
		<router-link :to="{path : '/outpatient/outpatient_integralShopDetails',query:{commodityId:commodityId,}}">
			<div class="settlement">
				<button>总计:&nbsp;&nbsp;<span>{{shopDetails.payExchangepoint}}</span>积分</button>
				<button>立即兑换</button>
			</div>
		</router-link>
	</div>
</template>

<script>
import qs from 'qs';
export default {
	name: 'integralShop',
	data () {
		return {
			shopDetails:{},
			commodityId:'',
		}
	},
	computed:{
	},
	components:{
	},
	created () {
	},
 	mounted() {
	},
	activated(){
		if(this.query != JSON.stringify(this.$route.query)){
			Object.assign(this.$data, this.$options.data());
			this.query = JSON.stringify(this.$route.query);
			if(window.plus){
				//plus.navigator.setStatusBarBackground("#ffffff");
				plus.navigator.setStatusBarStyle("dark")
			}
			this.getdata();
		}
  	},
	methods: {
		returnFn(){
			this.$router.back(-1);
		},
		getdata(){
			this.commodityId = this.$route.query.commodityId
			this.$axios.post('/clientend2/clinicend/pointexchange/commoditydetail',qs.stringify({
				clinicId : this.$store.state.outpatient.login.clinicId,
				commodityId : this.commodityId,
			}))
			.then(res => {
				if(!res.data.codeMsg){
					this.shopDetails = {
						name : res.data.data.name,
						payExchangepoint : res.data.data.payExchangepoint,
						stock : res.data.data.stock,
						intro : res.data.data.intro,
						cover : [],
						requestId : this.commodityId,
					};
					this.shopDetails.cover = res.data.data.cover.split(',')
				}else{
					this.$toast(res.data.codeMsg)
				}
			})
			.catch((err)=>{})
		}
	},
}
</script>

<style scoped>
.integralShop{
	width: 100%;
	height: 100%;
	position: relative;
}
.slip{
	width: 100%;
	position: relative;
}
>>>.van-swipe-item{
	float: left;
	height:3.75rem!important;
	width: 100%!important;
}
>>>.van-swipe-item>img{
	height:3.75rem;
	width: 100%;
	object-fit: cover;
}
.return{
	position: absolute;
	top: .18rem;
	left: .18rem;
	z-index: 999;
}
.return img{
	width: .09rem;
	height: .16rem;
}
.shopAbout{
	padding: .1rem .16rem;
}
.shopAbout h4{
	color: #333333;
	font-size: .14rem;
	font-weight: bolder;
}
.shopAbout p{
	color: #666666;
	font-size: .12rem;
}
.settlement{
	width: 84.8%;
	height: .4rem;
	position: absolute;
	bottom: .18rem;
	right: 7.6%;
	font-size: .15rem;
	color: #333333;
}
.settlement button:first-child{
	width: 57.5%;
	height: 100%;
	line-height: 100%;
	text-align: center;
	border-radius: .5rem 0rem 0rem .5rem ;
	background-color: #FFFFFF;
	border: none;
	padding: 0;
	box-shadow: rgba(0,0,0,.2) 0rem 0rem .05rem 0rem;
}
.settlement button:first-child span{
	color: #FF951B;
}
.settlement button:last-child{
	width: 41.19%;
	height: 100%;
	color: #FFFFFF;
	background-color: #2B77EF;
	border: none;
	padding: 0;
	border-radius: 0rem .5rem .5rem 0rem;
	box-shadow: hsla(0, 0%, 0%, 10%) 0rem 0rem .05rem 0rem;
  margin-left: -0.05rem;
}
</style>
