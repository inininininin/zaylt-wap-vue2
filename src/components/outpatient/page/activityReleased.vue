<template>
	<div class="active">
		<div class="topNav" :style="{'padding-top':$store.state.paddingTop}">
			<div class="leftImg" @click="goBackFn"  id="navback">
				<img src="../../../assets/image/shape@3x.png" alt="">
			</div>
			<div class="centerTitle">
				<h3>发布精准活动</h3>
			</div>
			<div class="right"></div>
		</div>
		<div class="zhangwei"></div>
		<!-- <router-link :to="{name:'outpatient_addActivity'}"> -->
		<div class="addActive" @click="$router.push({path:'/outpatient/outpatient_addActivity',query:{time: new Date().getTime().toString()}})" :style="{'padding-top':$store.state.paddingTop}">
			<span>+</span>
			<span>新建活动</span>
		</div>
		<!-- </router-link> -->
		<div class="_activeList" @scroll="handleScroll" ref="_activeList">
			<van-list v-model="loading" :finished="finished" finished-text="没有更多了" @load="onLoad">
				<van-swipe-cell v-for="(item,inx) in active" :key="inx"  :right-width= 65 >
					<van-cell :border="false" @click="$router.push({path:'/outpatient/outpatient_activityDetails',query:{itemId:item.itemId,time: new Date().getTime().toString()}})">
						<!-- <router-link :to="{path : '/outpatient/outpatient_activityDetails',query:{itemId:item.itemId,}}"> -->
						<div class="activeList">
							<img v-lazy="item.cover" alt="">
							<div class="activeTitle">
								<h4>{{item.title}}</h4>
								<span>{{moment(item.alterTime).format('YYYY-MM-DD HH:mm')}}</span>
							</div>
						</div>
						<!-- </router-link> -->
					</van-cell>
					<template slot="right">
						<button class="deleteStyle" @click="deleteActiviteFn(item)">
							<img src="../../../assets/image/activiteDelete.png" alt="">
						</button>
					</template>
				</van-swipe-cell>
			</van-list>
		</div>
		<div class="returnTop" @click="$refs._activeList.scrollTop=0;hospitalReturnTopPage = false;" ref="returnTopRef" v-show="hospitalReturnTopPage">
			<img src="../../../assets/image/returnTop.png" alt />
			<span>顶部</span>
		</div>
	</div>
</template>

<script>
import qs from 'qs';
export default {
	name: 'case',
	data () {
		return {
			active:[],
			loading: true,
			finished: false,
			page: 0,
			hospitalReturnTopPage:false,
			scrollTop:0
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
	activated(){
		if(this.query != JSON.stringify(this.$route.query)){
			Object.assign(this.$data, this.$options.data());
			this.query = JSON.stringify(this.$route.query);
			if(window.plus){
				//plus.navigator.setStatusBarBackground("#ffffff");
				plus.navigator.setStatusBarStyle("dark")
			}
			this.onLoad()
		}
		if(this.scrollTop != 0){
			this.$refs._activeList.scrollTop = this.scrollTop;
		}
    },
	methods: {
		// 滑动一定距离出现返回顶部按钮
		handleScroll() {
			this.scrollTop = this.$refs._activeList.scrollTop || this.$refs._activeList.pageYOffset
			if (this.scrollTop > 800) {
				this.hospitalReturnTopPage = true;
			} else {
				this.hospitalReturnTopPage = false;
			}
		},
		//回退方法
		goBackFn(){
			// this.$router.push({name:'hospital_clinic'})
			this.$router.back(-1)
		},
		deleteActiviteFn(_item){
			for(let i=0;i<this.active.length;i++){
				if(this.active[i].itemId ==_item.itemId){
				this.active.splice(i,1)
				i--;
				}
			}
			this.$axios.post('/c2/activity/itemdel',qs.stringify({
				itemId : _item.itemId,
			}))
			.then(res => {
				this.getdata();
			})
			.catch((err)=>{
				this.$toast('加载失败!')
			})
		},
		onLoad(){
			++this.page;
			this.getdata();
		},
		getdata(){
			this.$axios.post('/c2/activity/items',qs.stringify({
				hospitalId : this.$store.state.outpatient.login.hospital.hospitalId,
				pn: this.page,
				ps: 10
			}))
			.then(res => {
				// 
				if(res.data.data.items.length != 0){
					for(let i in res.data.data.items){
						this.active.push(res.data.data.items[i])
						// 
					}
					this.loading = false;
				}else {
					this.loading = false;
					this.finished = true;
				}
			})
			.catch((err)=>{
				this.$toast('加载失败!')
			})
		}
	},
}
</script>

<style scoped>
.active{
	width: 100%;
	height: 100%;
	overflow: hidden;
	background-color: #FFFFFF;
}
.topNav{
	width: 100%;
	height: .47rem;
	line-height: .47rem;
	background-color: #FFFFFF;
	position: fixed;
	top:0;
	z-index: 999;
}
.zhangwei{
	width: 100%;
	height: .47rem;
}
.leftImg{
	width: 10%;
	height: .47rem;
	float:left;
}
.leftImg img{
	width: .09rem;
	height: .15rem;
	line-height: .47rem;
	padding-left: .16rem;
}
.centerTitle{
	width: 80%;
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
	width: 10%;
	height: .47rem;
	line-height: .47rem;
	float:left;
}

.addActive{
	width: 93.6%;
	height: .49rem;
	line-height: .49rem;
	text-align: center;
	font-size: .14rem;
	margin: 0 auto;
	/* margin-top: .59rem; */
	background-color: #FFFFFF;
}
.addActive span:first-child{
	color: #2B77EF;
	width: .15rem;
	height: .15rem;
	line-height: .15rem;
	border: 1px solid #2B77EF;
	display: inline-block;
}
.addActive span:last-child{
	color: #2B77EF;
}
.activeList{
	width: 93.6%;
	height: 1.8rem;
	margin: .12rem auto;
	margin-right: 0rem;
	position: relative;
	overflow: hidden;
	box-sizing: border-box;
}
.activeList>img{
	height: 100%;
	width: 100%;
	object-fit: cover;
}
.activeTitle{
	position: absolute;
	color: #FFFFFF;
	bottom: 0rem;
	left: 0rem;
	width: 100%;
	height: 50%;
	background: linear-gradient(rgba(0, 0, 0, 0), rgba(0, 0, 0, 1));
}
.activeTitle h4{
	font-size: .15rem;
	position: absolute;
	bottom: .32rem;
	left: .2rem;
}
.activeTitle span{
	color: #EAF2FF;
	position: absolute;
	bottom: .15rem;
	left: .2rem;
}
>>>.van-cell {
    position: relative;
    width: 100%;
    padding:0px;
	font-size: .12rem;
	line-height: 100%;
	background-color: transparent;
}
.deleteStyle{
	margin:.12rem 0rem;
	height: 1.8rem;
	width: .6rem;
	border: none;
	color: #FFFFFF;
	background-color: #e91a1a;
}
.deleteStyle img{
	width: .15rem;
}
>>>.van-swipe-cell {
    position: relative;
    overflow: hidden;
    width: 93.6%;
}
._activeList{
	width: 100%;
	height: calc(100% - .96rem);
	touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
  	overflow: scroll;
  	overflow-x: hidden;
}
</style>
