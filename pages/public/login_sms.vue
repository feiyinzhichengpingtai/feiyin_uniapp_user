<template>
	<view class="container">
		<view class="left-bottom-sign"></view>
		<view class="back-btn yticon icon-zuojiantou-up" @click="navBack"></view>
		<view class="right-top-sign"></view>
		<!-- 设置白色背景防止软键盘把下部绝对定位元素顶上来盖住输入框等 -->
		<view class="wrapper">
			<view class="left-top-sign">LOGIN</view>
			<view class="welcome">
				欢迎回来！
			</view>
			<view class="input-content">
				<view class="number-input-item">
					<input 
						type="number" 
						:value="mobile" 
						placeholder="请输入手机号码"
						maxlength="11"
						data-key="mobile"
						@input="inputChange"
					/>
					<button class="primary" type="primary" style="text-align: center;vertical-align: middle;height: 80upx;font-size: 25upx;">发送验证码</button>
				</view>
				<view class="input-item">
					<text class="tit">短信验证码</text>
					<input 
						type="number" 
						value="" 
						placeholder="填写6位短信验证码"
						placeholder-class="input-empty"
						maxlength="6"
						password 
						data-key="smsCode"
						@input="inputChange"
						@confirm="toLogin"
					/>
				</view>
			</view>
			<button class="confirm-btn" @click="toLogin" :disabled="logining">登录</button>
			<view class="forget-section" @click="" v-show="false">
				忘记密码?
			</view>
			<view class="forget-section" @click="toRegist">
				新用户注册
			</view>
		</view>
		<view class="register-section">
			登录即代表您已同意我们的
			<text @click="toRegist">隐私政策条例</text>
		</view>
	</view>
</template>

<script>
	import {  
        mapMutations,
		mapState
    } from 'vuex';
	
	export default{
		data(){
			return {
				mobile: '',
				password: '',
				logining: false
			}
		},
		onLoad(){
			console.log(this.$userCenter);
			console.log(this.$userCenter+"api/v1/members/login?mode=verificationCode");
			
		},
		computed: {
			...mapState(['hasLogin','userInfo'])
		},
		methods: {
			...mapMutations(['login', 'logout']),
			inputChange(e){
				const key = e.currentTarget.dataset.key;
				this[key] = e.detail.value;
			},
			navBack(){
				uni.navigateBack();
			},
			toRegist(){
				this.$api.msg('去注册');
				uni.navigateTo({
					url: '/pages/public/register'
				});
			},
			async toLogin() {
				this.logining = true;
				this.showLoading();
				console.log("toLogin");
				const {
					mobile,
					smsCode
				} = this;
				
				console.log("mobile:"+mobile);
				console.log("smsCode:"+smsCode);

				
				//数据验证模块
				// if(!this.$api.match({
				// 	mobile,
				// 	password
				// })){
				// 	this.logining = false;
				// 	return;
				// }
			
				const [err, res] = await uni.request({
					url: this.$userCenter+"api/v1/members/login?mode=verificationCode",
					method: 'GET',
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						'deviceNo': '121',
						'channelNo': '2202',
						'systemNo': '0657'
					},
					data: {
						phone: mobile,
						smsCode: smsCode,
					}
				});
				if (err) {
					this.hideLoading();
					console.log('request fail', err);
					uni.showModal({
						content: err.errMsg,
						showCancel: false
					});
				} else {
					this.hideLoading();
					console.log('request success', res)
					uni.showToast({
						title: '请求成功',
						icon: 'success',
						mask: true,
						duration: 2000
					});
					console.log(JSON.stringify(res))
				}
				
				if (res.data.returnCode == 200000) {
					this.login(res.data.data);
					uni.navigateBack();
				} else {
					this.$api.msg(result.msg);
					this.logining = false;
				}
			},
			showLoading() {
				uni.showLoading({
					title: 'loading'
				});
				// #ifdef MP-ALIPAY
				this._showTimer && clearTimeout(this._showTimer);
				this._showTimer = setTimeout(() => {
					this.hideLoading();
				}, 3000)
				// #endif
			},
			hideLoading() {
				uni.hideLoading();
			},
		},
		

	}
</script>

<style lang='scss'>
	page{
		background: #fff;
	}
	.container{
		padding-top: 115px;
		position:relative;
		width: 100vw;
		height: 100vh;
		overflow: hidden;
		background: #fff;
	}
	.wrapper{
		position:relative;
		z-index: 90;
		background: #fff;
		padding-bottom: 40upx;
	}
	.back-btn{
		position:absolute;
		left: 40upx;
		z-index: 9999;
		padding-top: var(--status-bar-height);
		top: 40upx;
		font-size: 40upx;
		color: $font-color-dark;
	}
	.left-top-sign{
		font-size: 120upx;
		color: $page-color-base;
		position:relative;
		left: -16upx;
	}
	.right-top-sign{
		position:absolute;
		top: 80upx;
		right: -30upx;
		z-index: 95;
		&:before, &:after{
			display:block;
			content:"";
			width: 400upx;
			height: 80upx;
			background: #b4f3e2;
		}
		&:before{
			transform: rotate(50deg);
			border-radius: 0 50px 0 0;
		}
		&:after{
			position: absolute;
			right: -198upx;
			top: 0;
			transform: rotate(-50deg);
			border-radius: 50px 0 0 0;
			/* background: pink; */
		}
	}
	.left-bottom-sign{
		position:absolute;
		left: -270upx;
		bottom: -320upx;
		border: 100upx solid #d0d1fd;
		border-radius: 50%;
		padding: 180upx;
	}
	.welcome{
		position:relative;
		left: 50upx;
		top: -90upx;
		font-size: 46upx;
		color: #555;
		text-shadow: 1px 0px 1px rgba(0,0,0,.3);
	}
	.input-content{
		padding: 0 60upx;
	}
	.input-item{
		display:flex;
		flex-direction: column;
		align-items:flex-start;
		justify-content: center;
		padding: 0 30upx;
		background:$page-color-light;
		height: 120upx;
		border-radius: 4px;
		margin-bottom: 50upx;
		&:last-child{
			margin-bottom: 0;
		}
		.tit{
			height: 50upx;
			line-height: 56upx;
			font-size: $font-sm+2upx;
			color: $font-color-base;
		}
	}

	.number-input-item{
		display:flex;
		flex-direction: row;
		align-items: center;
		justify-content: center;
		padding-left: 30upx;
		background:$page-color-light;
		height: 120upx;
		border-radius: 4px;
		margin-bottom: 50upx;
		&:last-child{
			margin-bottom: 0;
		}
		input{
			height: 60upx;
			font-size: $font-base + 2upx;
			color: $font-color-dark;
		}	
	}

	.confirm-btn{
		width: 630upx;
		height: 76upx;
		line-height: 76upx;
		border-radius: 50px;
		margin-top: 70upx;
		background: $uni-color-primary;
		color: #fff;
		font-size: $font-lg;
		&:after{
			border-radius: 100px;
		}
	}
	.forget-section{
		font-size: $font-sm+2upx;
		color: $font-color-spec;
		text-align: center;
		margin-top: 40upx;
	}
	.register-section{
		position:absolute;
		left: 0;
		bottom: 50upx;
		width: 100%;
		font-size: $font-sm+2upx;
		color: $font-color-base;
		text-align: center;
		text{
			color: $font-color-spec;
			margin-left: 10upx;
		}
	}
</style>
