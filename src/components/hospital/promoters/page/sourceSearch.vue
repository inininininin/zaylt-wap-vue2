<template>
	<!-- <van-pull-refresh v-model="pullingDown" @refresh="afterPullDown"> -->
		<div>
			<div class="_search"  >
			<div class="top_search" :style="{'padding-top':$store.state.paddingTop}">
				<div class="search_return">
					<a @click="goBackFn"  id="navback">
						<img src="../../../../assets/image/shape@3x.png" alt />
					</a>
				</div>
				<div class="search_input">
						<img src="../../../../assets/image/sousuo@2x.png" alt />
					<input
						type="text"
						placeholder="搜索病员"
						v-model="keywords"
						v-focus="this.$route.query.focus"
						@keyup.enter="inputNow"
					/>
				</div>
				<div class="clinic_buttton" @click="inputNow">
					<button>搜索</button>
				</div>
				<div class="screening" @click="showPopup">
					<span>筛选</span>
					<img src="../../../../assets/image/screening.png" alt />
				</div>
				
			</div>
			<div class="_searchList" @scroll="handleScroll" ref="_searchList">
				<van-list  v-model="loading" :finished="finished" finished-text="没有更多了"  @load="nextPageFn">
					<ul class="list" :style="{'padding-top':$store.state.paddingTop}">
						<!--  -->
						<li v-for="(item,inx) in  items" :key="inx" @click="$router.push({path:'/promoters/promoters_detailsPage',query:{patientId : item.itemId,time: new Date().getTime().toString()}})">
							<!-- <router-link :to="{path : '/promoters/promoters_detailsPage' ,query : {patientId : item.itemId,}}"> -->
								<div class="style">
									<div class="contentTitle">
										<img :src="item.img" alt="">
										<span>{{item.realname}}</span>
									</div>
									<div class="contnet_left">
										<span>推送：{{moment(item.pushTime).format('YYYY-MM-DD')}}</span>
										<span>状态：{{item.span}}</span>
									</div>
								</div>
							<!-- </router-link> -->
							<div class="content_right">
								<button :class="item.buttonColor" @click="submitFn(item,$event)">{{item.button}}</button>
							</div>
						</li>
					</ul>
				</van-list>
			</div>
		</div>
		<div class="returnTop" @click="$refs._searchList.scrollTop=0;hospitalReturnTopPage = false;" ref="returnTopRef" v-show="hospitalReturnTopPage">
			<img src="../../../../assets/image/returnTop.png" alt />
			<span>顶部</span>
		</div>
		<van-popup v-model="show" position="right" :style="{ height: '100%',width:'75%'}">
			<div id="indexLabel">
			<div class="labelLabel" >
				<strong>状态</strong>
				<button  class="right" @click="labelLabelFn(0,$event)" :id="labelDocument[0]">未就诊</button>
				<button @click="labelLabelFn(1,$event)" :id="labelDocument[1]">已就诊</button>
			</div>
				<div class="labelLabel" >
					<strong>就诊时间</strong>
					<button class="rightLine" @click="labelLabelFn(2,$event)" :id="labelDocument[2]">
						{{Time.confirmStart?  moment(Time.confirmStart).format('YYYY-MM-DD'):'开始时间'}}
					</button>
					<button  @click="labelLabelFn(3,$event)" :id="labelDocument[3]">
						{{Time.confirmOver? moment(Time.confirmOver).format('YYYY-MM-DD'):'结束时间'}}
					</button>
				</div>
				<div class="labelLabel">
					<strong>推送时间</strong>
					<button class="rightLine"  @click="labelLabelFn(4,$event)"  :id="labelDocument[4]">
						{{Time.pushStart? moment(Time.pushStart).format('YYYY-MM-DD'):'开始时间'}}
					</button>
					<button  @click="labelLabelFn(5,$event)"  :id="labelDocument[5]">
						{{Time.pushOver? moment(Time.pushOver).format('YYYY-MM-DD'):'结束时间'}}
					</button>
				</div>
				<div class="LabelResult">
					<button @click="screeningResult">重选</button>
					<button @click="screeningSubmit">确定</button>
				</div>
			</div>
			
		</van-popup>
		<van-popup @click="closeFn" v-model="showTime" position="bottom" :style="{ height: '40%',width:'100%'}">
				<van-datetime-picker
				type="date"
				@confirm="confirm"
				@cancel="cancel"
				/>
		</van-popup>
	</div>
	<!-- </van-pull-refresh> -->
</template>

<script>
import qs from "qs";
import moment from 'moment'
import Vue from 'vue';
export default {
  name: "index_search",
  data() {
    return {
      timer: undefined,
	  items:[],
      keywords: "", //搜索框的关键字value
      pullingDown: false,
	  // lable的dom节点
	  labelDocument:['labelDocument','labelDocument2','labelDocument3','labelDocument4','labelDocument5','labelDocument6'],
	  //筛选数据
	  Time:{
	  	look:'',
	  	noLook:'',
	  	confirmStart : undefined,
	  	confirmOver : undefined,
	  	pushStart : undefined,
	  	pushOver : undefined,
	  	postState : undefined,
	  },
	  dateStata : '',
	  loading: true,
	  // 加载状态结束
	  finished: false,
	  page:0,
		 scrollTop:0,
		 hospitalReturnTopPage:false,
		 show:false,
    };
  },
  computed: {
    showTime: {
      get: function() {
        return this.$store.state.showTime;
      },
      set: function(newValue) {
        this.$store.state.showTime = newValue;
      }
    },
  },
  components: {
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
		this.show = this.$route.query.show
		if(this.scrollTop != 0){
			this.$refs._searchList.scrollTop = this.scrollTop;
		}
    },
  methods: {
	  // 滑动一定距离出现返回顶部按钮
		handleScroll() {
			this.scrollTop = this.$refs._searchList.scrollTop || this.$refs._searchList.pageYOffset
			if (this.scrollTop > 800) {
				this.hospitalReturnTopPage = true;
			} else {
				this.hospitalReturnTopPage = false;
			}
		},
    afterPullDown() {
      //下拉刷新
		setTimeout(() => {
			this.pullingDown = false;
			this.initData();
			
		}, 500);
    },
    initData() {
	  Object.assign(this.$data, this.$options.data());
	  this.nextPageFn()
    },
    //显示筛选弹窗
    showPopup() {
      this.show = 1;
    },
    //键盘输入值时触发
    inputNow(_keywordsCode) {
    	this.items = [];
    	this.finished = true;
		this.page = 0;
    	if(!this.keywords){
    		this.finished = false;
    		
    		this.nextPageFn(status,this.page);
    	}else{
    		this.nextPageFn(status,'');
    	}
    },
    goBackFn() {
      this.$router.back();
    },
    // 筛选确定
    screeningSubmit(){
		this.show = false;
		this.items = [];
		this.page = 0;
    	this.nextPageFn();
    },
    // 筛选重置
    screeningResult(){
		this.initData();
		document.getElementById(this.labelDocument[0]).style.backgroundColor = "#EEEEEE";
		document.getElementById(this.labelDocument[1]).style.backgroundColor = "#EEEEEE";
		document.getElementById(this.labelDocument[2]).style.backgroundColor = "#EEEEEE";
		document.getElementById(this.labelDocument[3]).style.backgroundColor = "#EEEEEE";
		document.getElementById(this.labelDocument[4]).style.backgroundColor = "#EEEEEE";
		document.getElementById(this.labelDocument[5]).style.backgroundColor = "#EEEEEE";
    },
    //选择框样式
    labelLabelFn(_vlaue,_this){
    	// 
    	// 
    	let buttonStyle = document.getElementById(this.labelDocument[_vlaue]);
    	switch(_vlaue){
    		case 0:
    		document.getElementById(this.labelDocument[0]).style.backgroundColor = "#EEEEEE";
    		document.getElementById(this.labelDocument[1]).style.backgroundColor = "#EEEEEE";
    		_this.target.style.backgroundColor = "#FFE1BE";
    		this.Time.look = "";
    		this.Time.noLook = "";
    		this.Time.look = '未就诊';
    		this.Time.postState = 1;
			this.dateStata=_vlaue;
    		// 
    
    		break;
    		case 1:
    		document.getElementById(this.labelDocument[0]).style.backgroundColor = "#EEEEEE";
    		document.getElementById(this.labelDocument[1]).style.backgroundColor = "#EEEEEE";
    		_this.target.style.backgroundColor = "#FFE1BE";
    		this.Time.look = "";
    		this.Time.noLook = "";
    		this.Time.noLook = '已就诊';
    		this.Time.postState = 4;
			this.dateStata=_vlaue;
    		// 
    		break;
    
    		case 2:
    		document.getElementById(this.labelDocument[2]).style.backgroundColor = "#EEEEEE";
    		document.getElementById(this.labelDocument[3]).style.backgroundColor = "#EEEEEE";
    		_this.target.style.backgroundColor = "#FFE1BE";
			this.dateStata=_vlaue;
    		this.Time.confirmStart = this.time;
    		this.showTime = true;
    		break;
    
    		case 3:
    		_this.target.style.backgroundColor = "#FFE1BE";
    		// 
			this.dateStata=_vlaue;
    		this.Time.confirmOver = this.time;
    		this.showTime = true;
    		break;
    
    		case 4:
    		_this.target.style.backgroundColor = "#FFE1BE";
    		// 
			this.dateStata=_vlaue;
    		this.Time.pushStart = this.time;
    		this.showTime = true;
    		break;
    
    		case 5:
    		_this.target.style.backgroundColor = "#FFE1BE";
    		this.dateStata=_vlaue;
    		// 
    		this.showTime = true;
    		this.Time.pushOver = this.time;
    		break;
    	}
    },
    //关闭半遮罩
    closeFn(){
    	// 
    	this.showTime = false;
    },
    // 确定选择的日期
    confirm(_value){
    	this.time = '';
    	// this.time = _value
    	let time = moment(_value).format('YYYY-MM-DD HH:mm:ss')
    	this.time = new Date(time).getTime();
    	// 
    	// 
    	switch (this.dateStata){
    		case 2:
    		this.Time.confirmStart = '';
    		this.Time.confirmStart = this.time;
    		break;
    		case 3:
    		this.Time.confirmOver = '';
    		this.Time.confirmOver = this.time;
    		break;
    		case 4:
    		this.Time.pushStart = '';
    		this.Time.pushStart = this.time;
    		break;
    		case 5:
    		this.Time.pushOver = '';
    		this.Time.pushOver = this.time;
    		break;
    	}
    },
    //取消选择的日期
    cancel(_value){
    	
    },
	// 获取下一页的方法
	getData(page){
		this.$axios.post('/c2/patient/items',qs.stringify({
				hospitalId: this.$store.state.hospital.login.hospital.hospitalId,
				hospitalUserId : this.$store.state.hospital.login.hospitalUserId,
				kw: this.keywords,
				status: this.Time.postState,
				pn : page,
				ps : 10,
				pushTimeStart : this.Time.pushStart,
				pushTimeEnd : this.Time.pushOver? this.Time.pushOver+(24*60*60*1000):this.Time.pushOver,
				hospitalConfirmTimeStart : this.Time.confirmStart,
				hospitalConfirmTimeEnd : this.Time.confirmOver? this.Time.confirmOver+(24*60*60*1000):this.Time.confirmOver,
			}))
			.then(res => {
				if(res.data.data.items.length != 0){
					for (let nums in res.data.data.items) {
						if(res.data.data.items[nums].status == 1){
							this.items.push({
								clinicName : res.data.data.items[nums].clinicName,
								itemId : res.data.data.items[nums].itemId,
								pushTime : res.data.data.items[nums].pushTime,
								realname : res.data.data.items[nums].realname,
								status : res.data.data.items[nums].status,
								img : require("../../../../assets/image/orange@2x.png"),
								button : "确认就诊",
								span : "未就诊"
							});
						}else if(res.data.data.items[nums].status == 4){
							this.items.push({
								clinicName : res.data.data.items[nums].clinicName,
								itemId : res.data.data.items[nums].itemId,
								pushTime : res.data.data.items[nums].pushTime,
								realname : res.data.data.items[nums].realname,
								status : res.data.data.items[nums].status,
								img :require( "../../../../assets/image/blue@2x.png"),
								button : "已就诊",
								buttonColor : "buttonColor",
								span : "已就诊"
							});
						}
					}
					// 加载状态结束
					this.loading = false;
				}else{
					this.loading = false;
					this.finished = true;
				}
			})
			.catch((err)=>{
				this.$toast(err)
			});
	},
	// 全部病原列表的下一页
	nextPageFn(){
		debugger;
		this.page++;
		this.getData(this.page);
	},
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
._search {
  position: relative;
  width: 100%;
  padding-top: 0.52rem;
  background-color: #ffffff;
}
.top_search {
  height: 0.5rem;
  width: 100%;
  background-color: #ffffff;
  position: fixed;
  top: 0;
  z-index: 999;
}
.search_return {
  height: 0.5rem;
  width: 11.3%;
  float: left;
}
.search_return a {
  height: 0.5rem;
  width: 100%;
}
.search_return a img {
  width: 0.09rem;
  height: 0.16rem;
  margin: 0.17rem 0.18rem;
}
.search_input {
  float: left;
  width: 63%;
  position: relative;
}
.search_input input {
  background-color: #f5f5f5;
  height: 0.335rem;
  line-height: 0.3rem;
  width: 79%;
  margin: 0.082rem 0rem;
  border: none;
  border-radius: 25px;
  padding-left: 0.37rem;
}
.search_input img {
  height: 0.15rem;
  width: 0.15rem;
  z-index: 3;
  position: absolute;
  top: 0.18rem;
  left: 0.15rem;
}
.clinic_buttton {
  float: left;
  margin-top: 0.125rem;
  margin-left: -0.05rem;
}
.clinic_buttton button {
  color: #ffffff;
  background-color: #2b77ef;
  border-radius: 0.15rem;
  border: none;
  height: 0.28rem;
  width: 0.45rem;
  font-size: 0.12rem;
}
.screening {
  float: left;
  width: 12.7%;
  height: 0.5rem;
  line-height: 0.5rem;
  margin-left: 0.05rem;
}
.screening span {
  margin-left: 0.05rem;
  font-size: 0.12rem;
  padding-top: 0.03rem;
  display: inline-block;
}
.screening img {
  height: 0.13rem;
  width: 0.12rem;
}
.content_right img {
  width: 0.11rem;
  height: 0.11rem;
  margin-right: 0.04rem;
}
>>>.van-pull-refresh{
  overflow: visible !important;
  -webkit-user-select: none;
  user-select: none;
}

#indexLabel {
  width: 85.5%;
  padding: 0.32rem 0.2rem 0.25rem 0.2rem;
  position: relative;
}
.labelLabel {
  height: 0.95rem;
}
.labelLabel:first-child {
  height: 0.95rem;
  border-bottom: 1px dotted rgba(0, 0, 0, 0.4);
}
.labelLabel strong {
  display: block;
}
.rightLine {
  margin-right: 0.25rem;
}
.rightLine:after {
  content: " ";
  position: absolute;
  height: 0.01rem;
  width: 0.15rem;
  bottom: 0;
  top: 50%;
  left: 107%;
  background-color: #999999;
}
.labelLabel button {
  height: 0.3rem;
  width: 1.05rem;
  margin-top: 0.1rem;
  border-radius: 0.15rem;
  border: none;
  background: #eeeeee;
  text-align: center;
  position: relative;
}
.labelLabel:first-child button:last-child {
  margin-left: 0.2rem;
}
.LabelResult {
  position: fixed;
  bottom: 0.25rem;
  right: 0.2rem;
}
.LabelResult button:first-child {
  border: none;
  height: 0.3rem;
  text-align: center;
  width: 0.8rem;
  border-radius: 100px 0px 0px 100px;
  background-color: #1ecac6;
}
.LabelResult button:last-child {
  border: none;
  height: 0.3rem;
  text-align: center;
  width: 0.8rem;
  border-radius: 0px 100px 100px 0px;
  background-color: #ff951b;
}
.list{
	width: 91.46%;
	margin: 0rem auto;
	margin-top: .1rem;
}
.list li{
	width: 100%;
	height: .845rem;
	box-shadow: 0px 0px 26px 3px hsla(0, 0%, 0%, 10%);
	margin-bottom: .1rem;
	position: relative;
	border-radius: .03rem;
}
.contentTitle{
	width: 100%;
	padding-top: .1rem;
	padding-left: .1rem;
	
}
.contentTitle img{
	width: .17rem;
	height: .17rem;
	margin-top: -.03rem;
}
.contentTitle span{
	font-size: .14rem;
	font-weight: bold;
}
.contnet_left{
	padding-left: .32rem;
	padding-top: .04rem;
}
.contnet_left span{
	display: block;
}
.content_right{
	position: absolute;
	right: .15rem;
	bottom: .25rem;
}
.content_right button{
	width: .8rem;
	height: .28rem;
	background-color: #2B77EF;
	border: none;
	border-radius: .14rem;
	color: #FFFFFF;
	font-size: .125rem;
}
.buttonColor{
    color: #333333!important;
    background-color: #EEEEEE!important;
}
.tabTitle{
	margin: .08rem 0rem;
}
.tabTitle span{
	display: block;
}
._searchList{
	width: 100%;
	height: calc(100vh - .5rem);
	touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
  	overflow: scroll;
  	overflow-x: hidden;
}
</style>
