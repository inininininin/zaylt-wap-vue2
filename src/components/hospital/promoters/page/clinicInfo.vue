<template>
	<div class="addClinic">
		<div class="navWarp" :style="{'padding-top':$store.state.paddingTop}">
			<div class="leftNav" @click="goBackFn"  id="navback">
				<img src="../../../../assets/image/back-white@2x.png" alt="">
			</div>
			<div class="centerNav">
				<span>门诊详情页</span>
			</div>
			<div class="rightNav">
				
			</div>
		</div>
		<div class="zhangwei"></div>
		<div class="content" :style="{'padding-top':$store.state.paddingTop}">
			<div class="newAdd">
				<div class="newAddTitle">
					<img src="../../../../assets/image/bitian@2x.png" alt="">
					<h3>必填项</h3>
					<ul class="Fill">
						<li>
							<span>门诊名称</span>
							<input type="text" v-model="addClinic.name"  placeholder="请填写" readonly="readonly">
						</li>
						<li>
							<span>推广人</span>
							<input type="text" maxlength="11" v-model="addClinic.hospitalUserName" placeholder="请填写" readonly="readonly">
						</li>
						<li>
							<span>分配账号</span>
							<input type="text" maxlength="11" v-model="addClinic.phone"  placeholder="请填写" readonly="readonly">
						</li>
						<li>
							<span>分配密码</span>
							<input type="password" v-model="addClinic.pwd " placeholder="" readonly="readonly">
						</li>
						<li>
							<span>确认密码</span>
							<input type="password" v-model="addClinic.pwdConfirm " placeholder="" readonly="readonly">
						</li>
						<li>
							<span>负责人</span>
							<input type="text"  v-model="addClinic.headmanName"  placeholder="请填写" readonly="readonly">
						</li>
						<li>
							<span>联系方式</span>
							<input type="text"  maxlength="11" v-model="addClinic.contactTel" oninput="value=value.replace(/[^\d]/g,'')" placeholder="请填写">
						</li>
						<li>
							<span>门诊地址</span>
							<input type="text" v-model="addClinic.address" placeholder="请填写" readonly="readonly">
						</li>
					</ul>
				</div>
				<div class="newAddTitle bottom">
					<img src="../../../../assets/image/xuantian@2x.png" alt="">
					<h3>选填项</h3>
					<ul class="Fill">
						<li>
							<span>备注</span>
							<input type="text" v-model="addClinic.remark" placeholder="请填写" readonly="readonly">
						</li>
						<li class="popup" @click="showFn">
							<span>营业执照</span>
							<img  id="backimg" :src='imageUpload'  alt="" >
						</li>
					</ul>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import qs from 'qs';
export default {
	name: 'search',
	data () {
		return {
			// 添加列表绑定数据
			addClinic:{
				name : '',
				phone : '',
				pwd : '',
				pwdConfirm : '',
				headmanName : '',
				contactTel : '',
				address : '',
				remark : '',
				license : '',
				readonly : '',
				clinicPromoterId : '',
				hospitalUserId : ''
			},
			// 上传图片弹窗显示
			imageUpload:'',
			value:''
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
			// 加载dom节点后,获取推广人列表请求
			this.getdata();
		}
    },
	methods: {
    getdata(){
      this.$axios.get('/hospital/operator/hospital-clinic/'+this.$route.query.clinicId)
      .then(_d => {
          this.addClinic = {
            name : _d.data.data.name,
            phone : _d.data.data.clinicUserPhone,
            pwd :'',
            headmanName : _d.data.data.headman,
            contactTel : _d.data.data.tel,
            address : _d.data.data.address,
            remark : _d.data.data.remark,
			hospitalUserName : _d.data.data.hospitalUserName,
          },
		// 
      	this.imageUpload = _d.data.data.license
      })
      .catch((err)=>{})
    },
		// 返回键
		goBackFn(){
			this.$router.back()
		},
		// 显示上传图片选择弹窗
		showFn(){
			this.show = true;
		},
		// 关闭上传图片选择弹窗
		closeFn() {
		      this.show = false;
		},
		changeFn(id){
			let promoter= this.option.find((n)=>n.value == id)
				this.addClinic.clinicPromoterId = promoter.clinicPromoterId
		},
	}
}
</script>

<style scoped>
.addClinic{
	width: 100%;
}
.navWarp{
	width: 100%;
	height: .48rem;
	line-height: .48rem;
	background-color: #2B77EF;
	color: #FFFFFF;
	position: fixed;
	top:0;
	z-index: 9999;
}
.zhangwei{
	width: 100%;
	height: .48rem;
}
.leftNav{
	float: left;
	width: 17.6%;
	height: .48rem;
	line-height: .48rem;
}
.leftNav img{
	width: .09rem;
	height: .15rem;
	margin-left: .16rem;
}
.centerNav{
	float: left;
	width: 64.8%;
	height: .48rem;
	line-height: .48rem;
	text-align: center;
}
.centerNav span{
	font-size: .16rem;
	font-weight: 600;
}
.rightNav{
	float: left;
	width: 17.6%;
	height: .48rem;
	line-height: .48rem;
}
.rightNav img{
	width: .14rem;
	height: .15rem;
	margin: 0rem .16rem 0rem .05rem;
}
.newAddTitle{
	width: 91.4%;
	margin-top: .2rem;
	margin: 0 auto;
	padding-top: .2rem;
}
.newAddTitle img{
	width: .165rem;
	height: .185rem;
}
.newAddTitle h3{
	margin-left: .05rem;
	width: .45rem;
	height: .21rem;
	display: inline;
}
.Fill {
	width:90%;
}
.Fill li{
	border: 1px solid #D8D8D8;
	border-radius: .02rem;
	padding: .12rem .15rem;
	margin-top:.12rem;
	width: 100%;
	position: relative;
}
.Fill li span{
	height: .21rem;width: .6rem;
}
.Fill li input{
	border: none;
	float:right;
	text-align: right;
	color: #2B77EF;
}

.bottom img{
	width: .15rem;
	height: .18rem;
}
.AlreadySpanColor{
	color: #2B77EF!important;
}
>>>.van-ellipsis {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
	color: #2B77EF;
}
>>>.van-dropdown-menu {
	float: right;
    height: .22rem;
    background-color: #fff;
    -webkit-user-select: none;
    user-select: none;
	color: #757575;
}
>>>.van-dropdown-menu__title {
    position: relative;
    box-sizing: border-box;
    max-width: 100%;
    padding: 0rem;
    color: #323233;
    font-size: .09rem;
    line-height: 18px;
}
>>>.van-dropdown-menu__title::after {
    position: absolute;
    top: 50%;
    right: -4px;
    margin-top: -5px;
    border: 3px solid;
    border-color: transparent transparent currentColor currentColor;
    -webkit-transform: rotate(-45deg);
    transform: rotate(-45deg);
    opacity: .8;
    content: none;
}
>>>.van-dropdown-item--down {
    bottom: 0;
    top: 202.141px!important;
}
.popup{
	height: .45rem;
	line-height: .45rem;
}
.rightImg{
	width: .06rem!important;
	height: .11rem!important;
	margin-top: .16rem;
	margin-left: .05rem;
	float: right;
}
#backimg{
	width: .43rem;
	height: 100%;
	margin: auto;
	float: right;
	object-fit: cover;
}
.popupChoose{
	height: .9rem;
	border-bottom: 6px solid #F5F5F5;
	text-align: center;
	font-size: .15rem;
}
.popupChoose span{
	display: block;
	width: 100%!important;
	height: .44rem!important;
	line-height: .44rem;
	border-bottom: .5px solid #D8D8D8;
}
.closeStyle{
	background: none;
	width: 100%;
	height: 100%;
	border: none;
	height: .44rem;
	font-size: .15rem;
}
.uploadPictures{
	position: absolute;
	width: 100%!important;
	height: .71rem!important;
	top: 0;
	left: 0;
}
.uploadPictures:hover{
   border-color:#3594F4;
}
.upload{
	opacity: 0;
	width: 100%!important;
	height: .44rem!important;
	position: absolute;
	top: 0;
	bottom: 0;
	left: 0;
	right: 0;

}
.add{
	display: block;
	width: 100%!important;
	height: .44rem!important;
	line-height: .44rem!important;
	/* margin-top:-.44rem

	; */
 }

</style>
