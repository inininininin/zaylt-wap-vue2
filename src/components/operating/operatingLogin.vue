<template>
  <div class="landing">
		<div class="nav">
        <span>— 运营端 —</span>
				<img src="../../assets/image/beijing.png" alt="">
		</div>
    <div class="typeNav" type="line" border="false">
      <div class="content">
      	<div class="inputBox">
      		<img class="telephoneImg" src="../../assets/image/iphone@2x.png" alt="">
      		<input type="text"  v-model="submitAccount.name" name='name' placeholder="请输入手机号">
          <img src="../../assets/image/X Copy@2x.png" alt="" class="closeImg" @click="emptyAccountFn('name')" v-if="submitAccount.name" >
      	</div>
      	<div class="inputBox">
      		<img  class="passwordImg" src="../../assets/image/mima@2x.png" alt="">
      		<input type="password" class="lastInput" v-model="submitAccount.password" name='password' placeholder="请输入密码" autocomplete id='pwd1' @keyup.enter="submit">
          <img :src='pwdImg' alt="" class="openImg" @click="numFN('pwd1')" v-if="submitAccount.password" >
          <img src="../../assets/image/X Copy@2x.png" alt="" class="closeImg" @click="emptyAccountFn('password')" v-if="submitAccount.password">
      	</div>
      	<div class="checkBox">
      		<input type="checkbox"
      		    class="input_check"
      		    :checked="checked"
      		    @change="changeFn"/>
      		<p>&nbsp;&nbsp;我已经阅读并同意
      		<!-- <a href="/oss/page/user-protocol.html">&nbsp;&nbsp;&lt;&lt;用户协议与隐私政策&gt;&gt;</a> -->
      			<router-link :to="{path : '/operating/operating_urlPage' ,query:{url : '/oss/page/user-protocol.html',name : '用户协议'}}">
      			《应用服务条款》
      			</router-link>
      		</p>
      	</div>
      	<button class="submitClass" type="submit" value="医院登录" @click="submit()">登录</button>
      	<div class="passwordReset">
      		<router-link  :to="{path : '/operating/operating_retrievePassword',query:{}}">
      			<div class="forget">
      				<span>忘记密码</span>
      				<img src="../../assets/image/wenhao@2x.png" alt="">
      			</div>
      		</router-link>
			
          	<div @click="chooseEntrance" class="returnTypePage">
          		<span style="color: #2B77EF;">选择端口</span>
          	</div>
      	</div>

      </div>
    </div>
  </div>
</template>

<script>
import {mapActions,mapGetters} from 'vuex'
import router from '../../router'
import qs from 'qs';
export default {
  name: 'account',
  data () {
    return {
    checked: true,
		submitAccount:{
			name: '',
			password: '',
      nameStata: false,
      passwordStata: false,
		},
		data:1,
    pwdImg : require('../../assets/image/close-eye@2x.png'),
    isLoginData:[],
    }
  },
  directives: {
    focus: {
		inserted: function (el, {value}) {
            if (value) {
                el.focus();
            }
        }
    }
  },
 
  mounted() {
    debugger
    let thisVue = this;
		if(window.plus){
      plus.navigator.setStatusBarStyle("dark")
    }
    
       if(!this.cookieOn()){
       this.$alert('您的浏览器限制了第三方Cookie, 这将影响您正常登录, 您可以更改浏览器的隐私设置, 解除限制后重试.', '提示', {
          confirmButtonText: '确定',
          
        });
    }

     $.ajax({
                  url:'/manager/login-refresh',
                  type:'post',
                  async:false,
                  success:function(res){
					if(res.codeMsg){
						console.log('s')
						thisVue.$toast(res.codeMsg)
					}
                    if(res.code == 0){
                      thisVue.$store.state.operating.login=res.data
                      thisVue.$toast({"message":'已登录',onClose(){
                          thisVue.$router.replace({ path : '/operating',query:{}});
                        }})
                    }
                  }
                })

    
		// let lastRoute = JSON.parse(localStorage.getItem('lastRoute'))
		//  if(this.$store.state.isLogin == 100){
		// 	this.$router.replace({ name : 'hospital_index',query:{}})
		// 	this.$router.push(lastRoute)
		// }else  if(this.$store.state.isLogin == 200){
		// 	this.$router.replace({ name : 'outpatient_index',query:{}})
		// 	this.$router.push(lastRoute)
		// }else  if(this.$store.state.isLogin == 300){
		// 	this.$router.replace({ name : 'chooseTheType',query:{}})
		// 	this.$router.push(lastRoute)
		// }
  },
  activated(){
    debugger
  },
  computed:{
    account:{
    	get: function() {
    		
    	    return this.$store.state.account
    	},
    	set: function (newValue) {
    		this.$store.state.account = newValue;
    	},
    },
    isLogin: {
      get: function() {
        return this.$store.state.isLogin
      },
      set: function (newValue) {
        this.$store.state.isLogin = newValue;
      },
    },
  },
  methods:{

	clear(){
		localStorage.clear('entrance')
	},
    chooseEntrance(){
      localStorage.removeItem('entrance');
	  debugger
      this.$router.push({path:'/',query:{}})
    },
    emptyAccountFn(value){
      if(value == 'name'){
        this.submitAccount.name = '';

      }else{

      this.submitAccount.password = '';
      }
    },
    numFN(_ref){
      // 
      // 
      if(document.getElementById(_ref).type == 'password'){
        document.getElementById(_ref).setAttribute('type','text')
        this.pwdImg = require('../../assets/image/open-eye@2x.png')
      }else{
        document.getElementById(_ref).setAttribute('type','password')
        this.pwdImg = require('../../assets/image/close-eye@2x.png')
      }
      if(document.getElementById(_ref).type == 'text'){
      }
    },
    submit(){
      let thisVue = this
    	 if(this.checked == true){
        this.$axios.post('/manager/login',qs.stringify({
        		account : this.submitAccount.name,
        		password : this.submitAccount.password
        	}))
        	.then( res =>{
            // 
            if(res.data.code == 0){
         $.ajax({
                  url:'/manager/login-refresh',
                  type:'post',
                  async:false,
                  success:function(res){
					if(res.codeMsg){
						console.log('s')
						thisVue.$toast(res.codeMsg)
					}
                    if(res.code == 0){
                      thisVue.$store.state.operating.login=res.data
                       thisVue.$toast({"message":'登录成功',onClose(){
                           thisVue.$router.replace({ path : '/operating/operating_index',query:{time:new Date().getTime().toString()}});
                        }})
                    }
                  }
                })
            }else{
              this.$toast(res.data.codeMsg);
            }
        	})
        	.catch((err)=>{
        		
        		this.$toast(err);
        	})
        }else{
          this.$toast('请勾选《应用服务条款》')
        }
    },
    changeFn(_value){
      // 
    	this.checked = _value.target.checked;
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.account{
	width: 100%;
	height: 100%;
}
.nav{
	width: 100%;
	height: 2.07rem;
	/* background: #2B77EF; */
  margin-bottom: .47rem;
  position: relative;
  /* background: url("../../assets/image/beijing.png")100% 100% center no-repeat; */
}
.nav span{
  /* display: block; */
  width: 1.1rem;
  height: .3rem;
  line-height: .3rem;
  color: #FFFFFF;
  position: absolute;
  right: 0;
  bottom: .4rem;
  left: 0;
  z-index: 999;
  margin:0rem  auto;
  text-align: center;
  font-weight: bolder;
  font-size: .18rem;
}
.nav img{
	height: 100%;
  width: 100%;
  /* z-index: 999; */
  position: absolute;
  top:0;
  right: 0;
  bottom: 0;
  left: 0;
  margin: auto;
}
.typeNav{
	padding: 0 7.5%;
	/* position: absolute; */
	top: 1.63rem;
	width: 85%;
  /* margin-to:   ;p: -.5rem; */
}
.content{
	width: 100%;
	margin-top: .47rem;
}
.submitClass{
	/* background-color: #2B77EF; */
  background-image: linear-gradient(to left, #F75178, #ED2828);
	width: 100%;height: .45rem;
	border-radius: .23rem;
	margin-top: .8rem;
	color: #FFFFFF;
	border: none;
	font-size: .19rem;
}
.inputBox{
	position: relative;
	width: 100%;
	margin: .2rem auto;
}

.passwordImg{
	position: absolute;
	height: .16rem;
	width: .18rem;
	left: 7%;
  top: 0;
  bottom: 0;
  margin: auto 0rem;
}
.telephoneImg{
	width: .13rem;
	height: .2rem;
	/* top:.35rem; */
	position: absolute;
	left: 7%;
  top: 0;
  bottom: 0;
  margin: auto 0rem;
}
.closeImg{
  width: .11rem;
  height: .11rem;
  position: absolute;
  right: 7%;
  top:0;
  bottom: 0;
  margin: auto 0;
}
.openImg{
  width: .15rem;
  height: .11rem;
  position: absolute;
  right: 14%;
  top:0;
  bottom: 0;
  margin: auto 0;
}
.inputBox input{
	width: 68%;
	height: .45rem;
	border-radius: .25rem;
	border: 1px solid #E5E5E5;
	padding-left: 15%;
	/* background: #F5F5F5; */
  font-size: .17rem;
  padding-right: 15%;
}
.lastInput{
  width: 63%!important;
  padding-right: 20%!important;
  text-align: left;
}
>>>.van-hairline--top-bottom{
	display: block;
   width: 100%!important;
   padding-bottom:.07rem;
}
.checkBox input[type=checkbox] {
  width: .17rem;
  height: .17rem;
  -webkit-appearance: none;
  background-color: transparent;
  border: 0;
  outline: 0 !important;
  color: #d8d8d8;
  position: relative;
  margin: 0!important;
}
.checkBox input[type=checkbox]:before{
  content: "";
  display:inline-block;
  width: .17rem;
  height: .17rem;
  background: url('../../assets/image/Rectangle29.png');
  box-sizing:border-box;
  border-radius: 3px;
  position: absolute;
  background-size: cover;
}

.checkBox input[type=checkbox]:disabled:before{
  content: "";
  display:inline-block;
  width: .17rem;
  height: .17rem;
  background-color: #333;
  box-sizing:border-box;
  border-radius: 3px;
  position: absolute;
}
.checkBox input[type=checkbox]:checked:before{
  content: "";
  display:inline-block;
  width: .17rem;
  height: .17rem;
  border: 1px dotted #D2A47E;
  background-image: url('../../assets/image/select.png');
  background-size: .17rem .17rem;
  box-sizing:border-box;
  border-radius: 3px;
  position: absolute;
}
.checkBox input[type=checkbox]:checked:after{
  content: "";
  display:inline-block;
  width: .17rem;
  height:.17rem;
  border-left: 0rem dotted #fff;
  border-top: 0rem dotted #fff;
  box-sizing:border-box;
  position: absolute;
  transform: rotate(-135deg) translate(-70%, 25%);
}
.checkBox{
	margin-top: .2rem;
	margin-right: .09rem;
	height: .3rem;
	line-height: .3rem;
	position: relative;
}
.checkBox input{
	position: absolute;
	top: .04rem;
}
.checkBox p{
	margin-top: -.05rem;
	display: inline;
	height: .17rem;line-height: .17rem;
}
.checkBox p a{
	color: #5ab5fc;
}
.passwordReset{
	 margin-top: .09rem;
	/* height: .17rem; */
}
.passwordRese{
	height: .21rem;
}
.passwordReset a{
	margin-right: .05rem;
	color: #2B77EF;
}
.passwordReset img{
	width: .15rem;
	height: .15rem;
}
.forget{
	text-align: right;
	width: .95rem;
  height: .25rem;
  line-height: .25rem;
	text-align: center;
	float: left;
  position: relative;
  text-align: left;
  /* display: inline; */
}
.forget span{
  /* width: .8rem; */
  font-size: .185rem;
}
.forget img{
  position: absolute;
  right: 0rem;
  top: 0rem;
  bottom: 0rem;
  margin: auto 0rem;
  display: inline;
}
.returnTypePage{
  float: right;
  display: inline
}
.returnTypePage{
  font-size: .185rem;
}
</style>
