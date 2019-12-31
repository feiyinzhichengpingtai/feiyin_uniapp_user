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
					<text class="attr">¥{{item.price}}</text>
					<text class="attr">1件</text>
					<!-- <text class="newPrice">¥{{item.price}}</text> -->
				   </view>
			 
					<view class='logInf'>
						<view class='view1'>
							<text class='text1'>承运来源:</text>
							<text class='text2'>顺风快递</text>
						</view>
						<view class='view1'>
							<text class='text1'>物流单号:</text>
							<text class='text2'>711111111111111111</text>
							<text bindtap="copyText" :data-text="item.price" class='copy'>复制</text>
						</view>  
					</view>
				</view>  
				</block>
			</view>
				<view class = "viewbottom">
			  <text class="goodsbottom">很抱歉，暂时无法显示实时物流信息，您可以复制单号后点击下面的链接查询</text>
			</view>
                <button class="add-btn" @click="confirm">查询物流</button>
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
	}
	/* 购物车列表项 */
	.cart-item{
		display:flex;
		position:relative;
		padding:20upx 40upx;
		height: 300upx;
		.image-wrapper{
			width: 130upx;
			height: 130upx;
			flex-shrink: 0;
			position:relative;
			image{
				width: 130upx;
				height: 130upx;
				border-radius:8upx;
			}
		}
		.item-right{
			display:flex;
			flex-direction: column;
			flex: 1;
			overflow: hidden;
			position:relative;
			padding-left: 30upx;

			.title,.price,.newPrice{
				font-size:$font-base + 2upx;
				color: $font-color-dark;
				height: 40upx;
			}
			.attr{
				font-size: $font-sm + 2upx;
				color: $font-color-light;
				height: 50upx;
			}
			.price,.newPrice{
				flex-direction: row;
				height: 50upx;
			}
		}
		.logInf{
		        padding-top: 30upx;
		        position: absolute;
				top: 160upx;
		        }
		       .view1{
				    display:flex;
				    flex-direction: row;
					flex-wrap: wrap;
					overflow: hidden;
					height: 50upx;
					font-size: 28upx;
					.text1,.text2{
						color: #999999;
						margin-right: 20upx
					}
					.copy{
						
					}
		       }	
	} 
     .goodsbottom{
	  font-size:12px;
	  margin:5px 5px; 
	 }
	 .viewbottom {
	  display: flex;
	  flex-direction: column;
	  align-items: center;
	  margin-top: 5px;
	  margin-bottom: 10px;
	  margin-left: 10px;
	  margin-right: 10px;
	 }
	 .add-btn{
	 	display: flex;
	 	align-items: center;
	 	justify-content: center;
	 	width: 690upx;
	 	height: 80upx;
	 	margin: 60upx auto;
	 	font-size: $font-lg;
	 	color: #fff;
	 	background-color: $base-color;
	 	border-radius: 10upx;
	 	box-shadow: 1px 2px 5px rgba(219, 63, 96, 0.4);
	 }
</style>
