<template>
	<view class="container">
	
			<view class="cart-list">
				<block v-for="(item, index) in cartList" :key="item.id">
					<view
						class="cart-item" 
						:class="{'b-b': index!==cartList.length-1}"
					>
			  
					<view class="image-wrapper">
							<image :src="item.image" 
								:class="[item.loaded]"
								mode="aspectFill" 
								lazy-load 
								@load="onImageLoad('cartList', index)" 
								@error="onImageError('cartList', index)"
							></image>
					</view>
					
				   <view class="item-right">
					<text class="clamp title">{{item.title}}</text>
					<text class="attr">{{item.attr_val}}</text>
					<!-- <text class="del-btn yticon icon-fork" @click="deleteCartItem(index)"></text> -->
					<text class="price">¥{{item.price}}</text>
					<text class="price">¥{{item.price}}</text>
				   </view>
			 
		<!-- 			<view class='logInf'>
						<view class='view1'>
							<text class='text1'>承运来源:</text>
							<text class='text2'>顺风快递</text>
						</view>
						<view class='view1'>
							<text class='text1'>物流单号:</text>
							<text class='text2'>711111111111111111</text>
							<text bindtap="copyText" :data-text="item.price" class='copy'>复制</text>
						</view>  
					</view> -->
				</view>  
				</block>
			</view>
			<view class = "viewbottom">
			  <text class="goodsbottom">很抱歉，暂时无法显示实时物流信息，您可以复制单号后点击下面的链接查询</text>
			</view>
             
	</view>
</template>

<script>
	import {
		mapState
	} from 'vuex';
	import uniNumberBox from '@/components/uni-number-box.vue'
	export default {
		components: {
			uniNumberBox
		},
		data() {
			return {
				total: 0, //总价格
				allChecked: false, //全选状态  true|false
				empty: false, //空白页现实  true|false
				cartList: [],
				text:['很抱歉，暂时无法显示实时物流信息']
			};
		},
		onLoad(){
			this.loadData();
		},
		watch:{
			//显示空白页
			cartList(e){
				let empty = e.length === 0 ? true: false;
				if(this.empty !== empty){
					this.empty = empty;
				}
			}
		},
		computed:{
			...mapState(['hasLogin'])
		},
		methods: {
			//请求数据
			async loadData(){
				let list = await this.$api.json('cartList'); 
				let cartList = list.map(item=>{
					item.checked = true;
					return item;
				});
				this.cartList = cartList;
				this.calcTotal();  //计算总价
			},
			//监听image加载完成
			onImageLoad(key, index) {
				this.$set(this[key][index], 'loaded', 'loaded');
			},
			//监听image加载失败
			onImageError(key, index) {
				this[key][index].image = '/static/errorImage.jpg';
			},
			navToLogin(){
				uni.navigateTo({
					url: '/pages/public/login'
				})
			}
			}
			
			
	}
</script>

<style lang='scss'>
	.container{
		padding-bottom: 134upx;
		/* 空白页 */
		.empty{
			position:fixed;
			left: 0;
			top:0;
			width: 100%;
			height: 100vh;
			padding-bottom:100upx;
			display:flex;
			justify-content: center;
			flex-direction: column;
			align-items:center;
			background: #fff;
			image{
				width: 240upx;
				height: 160upx;
				margin-bottom:30upx;
			}
			.empty-tips{
				display:flex;
				font-size: $font-sm+2upx;
				color: $font-color-disabled;
				.navigator{
					color: $uni-color-primary;
					margin-left: 16upx;
				}
			}
		}
	}
	/* 购物车列表项 */
	.cart-item{
		display:flex;
		position:relative;
		padding:30upx 40upx;
		.image-wrapper{
			width: 230upx;
			height: 230upx;
			flex-shrink: 0;
			position:relative;
			image{
				border-radius:8upx;
			}
		}
		.checkbox{
			position:absolute;
			left:-16upx;
			top: -16upx;
			z-index: 8;
			font-size: 44upx;
			line-height: 1;
			padding: 4upx;
			color: $font-color-disabled;
			background:#fff;
			border-radius: 50px;
		}
		.item-right{
			display:flex;
			flex-direction: column;
			flex: 1;
			overflow: hidden;
			position:relative;
			padding-left: 30upx;
			.title,.price{
				font-size:$font-base + 2upx;
				color: $font-color-dark;
				height: 40upx;
				line-height: 40upx;
			}
			.attr{
				font-size: $font-sm + 2upx;
				color: $font-color-light;
				height: 50upx;
				line-height: 50upx;
			}
			.price{
				height: 50upx;
				line-height:50upx;
			}
		}
	
		.del-btn{
			padding:4upx 10upx;
			font-size:34upx; 
			height: 50upx;
			color: $font-color-light;
		}
		
	}
	.logInf{
	        height: 100rpx;
	        padding:15rpx 30rpx;
	        position: relative;
	        }
	       .logInf .view1{
	        height: 50%;
	        font-size: 28rpx;
	        line-height: 50rpx
	       }
	       .text1{
	        color: #999999;
	        margin-right: 20rpx
	       }
		   .copy{
	      font-size: 22rpx;
	      color: #999;
	      display: inline-block;
	      width: 100rpx;
	      height: 40rpx;
	      position: relative;
	      bottom: 20rpx;
	      right: 0rpx; 
	      margin-left:-10px; 
	      border: 1px solid #999999;
	      text-align: center;
	      line-height: 40rpx;
	}
	.goodsbottom{
	  font-size:12px;
	  padding:0 2px 0 2px; 
	 }
	 .viewbottom {
	  display: flex;
	  flex-direction: column;
	  align-items: center;
	  margin-top: 5px;
	  margin-bottom: 10px;
	 }
</style>
