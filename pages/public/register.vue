<template>
	<view class="container">
		<view class="left-bottom-sign"></view>
		<view class="back-btn yticon icon-zuojiantou-up" @click="navBack"></view>
		<view class="right-top-sign"></view>
		<!-- 设置白色背景防止软键盘把下部绝对定位元素顶上来盖住输入框等 -->
		<view class="wrapper">
			<view class="left-top-sign">LOGIN</view>
			<view class="welcome">
				<view class= "photo">
					<image src="../../static/errorImage.jpg"></image>
				</view>
			</view>
			<view class="input-content">
				<view class="input-item">
					<text class="tit">中国+86</text>
					<view class="input-item-row">
						<input
							type="number" 
							:value="mobile" 
							placeholder="请输入手机号码"
							maxlength="11"
							data-key="mobile"
							@input="inputChange"
						/>
						<button class="mini-btn" type="primary">发送验证码</button>
					</view>
				</view>
				<view class="input-item">
					<text class="tit">短信验证码</text>
					<input 
						type="mobile" 
						value="" 
						placeholder="8-18位不含特殊字符的数字、字母组合"
						placeholder-class="input-empty"
						maxlength="20"
						password 
						data-key="password"
						@input="inputChange"
						@confirm="toLogin"
					/>
				</view>
				<view class="input-item">
					<text class="tit">密码(8到20位字符，包含字母和数字)</text>
					<input 
						type="mobile" 
						value="" 
						placeholder=""
						placeholder-class="input-empty"
						maxlength="20"
						password 
						data-key="password"
						@input="inputChange"
						@confirm="toLogin"
					/>
				</view>
				<view class="input-item">
					<text class="tit">请再次输入密码</text>
					<input 
						type="mobile" 
						value="" 
						placeholder=""
						placeholder-class="input-empty"
						maxlength="20"
						password 
						data-key="password"
						@input="inputChange"
						@confirm="toLogin"
					/>
				</view>
			</view>
			<view class="forget-section">
				已有账号，立即登录
			</view>
			<view class="agreement-section">
				<checkbox-group>
					<label>
						<checkbox value="cb" checked="true" style="transform:scale(0.7)"/>已阅读并同意《用户使用协议》
					</label>
				</checkbox-group>
			</view>
			<button class="confirm-btn" @click="toLogin" :disabled="logining">确认注册</button>
		</view>
		<view class="register-section">
			登录即代表您已同意我们的<text @click="toRegist">商业服务</text>和
			<text @click="toRegist">隐私政策条例</text>
		</view>
	</view>
</template>

<script>
	import {  
        mapMutations  
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
			
		},
		methods: {
			...mapMutations(['login']),
			inputChange(e){
				const key = e.currentTarget.dataset.key;
				this[key] = e.detail.value;
			},
			navBack(){
				uni.navigateBack();
			},
			toRegist(){
				this.$api.msg('去注册');
			},
			async toLogin(){
				this.logining = true;
				const {mobile, password} = this;
				/* 数据验证模块
				if(!this.$api.match({
					mobile,
					password
				})){
					this.logining = false;
					return;
				}
				*/
				const sendData = {
					mobile,
					password
				};
				const result = await this.$api.json('userInfo');
				if(result.status === 1){
					this.login(result.data);
                    uni.navigateBack();  
				}else{
					this.$api.msg(result.msg);
					this.logining = false;
				}
			}
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
	.welcome{
		position:relative;
		left: 0upx;
		top: -150upx;
		font-size: 46upx;
		color: #555;
		text-shadow: 1px 0px 1px rgba(0,0,0,.3);
		
	}
	.photo{
		display: flex;
		justify-content: center;
		align-items: center;
		image{
			text-align: center;
			width: 250upx;
			height: 250upx;
			
		}
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
	.title_image_view{
		display: flex;
		align-items: center; 
		justify-content: center;
		padding: 0 60upx;
		image{
			height : 150upx;
			width: 150upx;
		}
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
		.tit{
			height: 50upx;
			line-height: 56upx;
			font-size: $font-sm+2upx;
			color: $font-color-base;
		}
		input{
			height: 60upx;
			font-size: $font-base + 2upx;
			color: $font-color-dark;
			width: 100%;
		}
			
	}

	.input-item-row{
		display: flex;
		flex-direction: row;
		align-items:center;
	}

	.btn{
		height: 50upx;
		line-height: 90upx;
		font-size: $font-sm+1upx;
		color: $font-color-base;
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
		display: flex;
		justify-content: flex-end;
		font-size: $font-sm+2upx;
		color: $font-color-spec;
		text-align: center;
		margin-top: 20upx;
		padding: 0 60upx;
	}
	
	.agreement-section{
		padding: 0 60upx;
		margin-top: 20upx;
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
