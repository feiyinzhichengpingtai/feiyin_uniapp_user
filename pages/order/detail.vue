<template>
	<view class="content">
		<view class="right">
			<view class="navbar">
				<text class="spec">{{text1}}</text>	
			</view>
		<!-- 	<view class="navbar"> -->
				<text class="spec">{{text2}}</text>
			<!-- </view> -->
		</view>
		<!-- 地址 -->
		<navigator url="/pages/address/address?source=1" class="address-section">
			<view class="order-content">
				<text class="yticon icon-shouhuodizhi"></text>
				<view class="cen">
					<view class="top">
						<text class="name">{{addressData.name}}</text>
						<text class="mobile">{{addressData.mobile}}</text>
					</view>
					<text class="address">{{addressData.address}} {{addressData.area}}</text>
				</view>
				<text class="yticon icon-you"></text>
			</view>
		
		</navigator>
		<!-- <view class="navbar">
			<view 
				v-for="(item, index) in navList" :key="index" 
				class="nav-item" 
				:class="{current: tabCurrentIndex === index}"
				@click="tabClick(index)"
			>
				{{item.text}}
			</view>
		</view> -->

		<swiper :current="tabCurrentIndex" class="swiper-box" duration="300" @change="changeTab">
			<swiper-item class="tab-content" v-for="(tabItem,tabIndex) in navList" :key="tabIndex" catchtouchmove="stopTouchMove">
				<scroll-view 
					class="list-scroll-content" 
					scroll-y
				>
				<!-- @scrolltolower="loadData" -->
					<!-- 空白页 -->
					<empty v-if="tabItem.loaded === true && tabItem.orderList.length === 0"></empty>
					
					<!-- 订单列表 -->
					<view 
						v-for="(item,index) in tabItem.orderList" :key="index"
						class="order-item"
					>
						<!--<view class="i-top b-b">
							 <text 
								v-if="item.state===9" 
								class="del-btn yticon icon-iconfontshanchu1"
								@click="deleteOrder(index)"
							></text>
							<text
								v-if="item.state===8" 
								class="del-btn yticon icon-iconfontshanchu1"
								@click="deleteOrder(index)"
							></text> 
						</view>-->
						
						<view v-if="item.goodsList.length > 1">
							<view 
								v-for="(goodsItem, goodsIndex) in item.goodsList" :key="goodsIndex"
								class="goods-box-single"
							>
								<image class="goods-img" :src="goodsItem.image" mode="aspectFill"></image>
								<view class="right">
									<text class="title clamp">{{goodsItem.title}}</text>
									<text class="attr-box">{{goodsItem.attr}}  x {{goodsItem.number}}</text>
									<text class="price">{{goodsItem.price}}</text>
									
									<text
										v-if="item.state===3" 
										class="action-btn1"
										@click="returnItem(item)"
									>退款</text>
									<!-- <button class="action-btn1" @click="returnItem(item)">退款</button> -->
									
								</view>
								<!-- <view>
									<button class="action-btn" @click="cancelOrder(item)">退款</button>	
								</view> -->
							</view>
						</view>
						
						<view
							v-if="item.goodsList.length === 1" 
							class="goods-box-single"
							v-for="(goodsItem, goodsIndex) in item.goodsList" :key="goodsIndex"
						>
							<image class="goods-img" :src="goodsItem.image" mode="aspectFill"></image>
							<view class="right">
								<text class="title clamp">{{goodsItem.title}}</text>
								<text class="attr-box">{{goodsItem.attr}}  x {{goodsItem.number}}</text>
								<text class="price">{{goodsItem.price}}</text>
								<text
									v-if="item.state!=1" 
									class="action-btn1"
									@click="returnItem(item)"
								>退款</text>
							</view>
						</view>
						<view class="mym"></view>
						<view class="yt-list1">
							<view class="yt-list-cell1 b-b">
								<text class="cell-tit clamp">商品总价</text>
								<text class="cell-tip">￥179.88</text>
							</view>
							<view class="yt-list-cell1 b-b">
								<text class="cell-tit clamp">配送方式</text>
								<text class="cell-tip"><!-- {{giveType}} -->普通快递 ￥10.00</text>
							</view>
						</view>
						<view class="yt-list1" >
							<view class="yt-list-cell1 b-b">
								<text class="cell-tit clamp">买家留言</text>
								<text class="cell-tip">无</text>
							</view>
							<view class="yt-list-cell1 b-b">
								<text v-if="item.state!=1" class="cell-tit clamp">实付金额</text>
								<text v-if="item.state!=1" class="cell-tip">￥189.88</text>
								<text v-if="item.state===1" class="cell-tit clamp">需付金额</text>
								<text v-if="item.state===1" class="cell-tip">￥189.88</text>
							</view>
						</view>
					    <view class="mym"></view>
						<view class="yt-list1">
							<view class="yt-list-cell1 b-b">
								<text class="cell-tit clamp">订单编号</text>
								<text class="cell-tip">11122233344455678990</text>
							</view>
							<view class="yt-list-cell1 b-b">
								<text class="cell-tit clamp">下单时间</text>
								<text class="cell-tip">2019-12-7 14:40:54</text>
							</view>
							<view v-if="item.state!=1" class="yt-list-cell1 b-b">
								<text class="cell-tit clamp">付款时间</text>
								<text class="cell-tip">2019-12-7 14:40:59</text>
							</view>
							<view v-if="item.state!=1" class="yt-list-cell1 b-b">
								<text class="cell-tit clamp">付款方式</text>
								<text class="cell-tip">支付宝</text>
							</view>
							<view v-if="item.state===3" class="yt-list-cell1 b-b">
								<text class="cell-tit clamp">发货时间</text>
								<text class="cell-tip">2019-12-8 00:40:54</text>
							</view>
						</view>
						 <view class="mym"></view>
						<!-- 底部 -->
						<view class="footer">
							<view class="action-box b-t" v-if="item.state === 1">
								<button class="action-btn" @click="cancelOrder('show')">取消订单</button>
								<button class="action-btn recom" @click="payOrder(item)">付款</button>
							</view>
							<view class="action-box b-t" v-if="item.state === 2">
								<button class="action-btn" @click="applicate(item)">申请开票</button>
								<!-- <button class="action-btn" @click="check(item)">查看物流</button>
								<button class="action-btn recom" @click="deleteOrder(index)">确认收货</button> -->
							</view>
							<view class="action-box b-t" v-if="item.state === 3">
								<button class="action-btn" @click="applicate(item)">申请开票</button>
								<button class="action-btn" @click="check(item)">查看物流</button>
								<button class="action-btn recom" @click="confirmOrder(item)">确认收货</button>
							</view>
							<view class="action-box b-t" v-if="item.state === 8">
								<button class="action-btn" @click="applicate(item)">申请开票</button>
								<button class="action-btn" @click="check(item)">查看物流</button>
								<button class="action-btn recom" @click="deleteOrder(index)">删除订单</button>
							</view>
							<view class="action-box b-t" v-if="item.state === 9">
								<button class="action-btn" @click="deleteOrder(index)">删除订单</button>
								<!-- <button class="action-btn" @click="check(item)">查看物流</button>
								<button class="action-btn recom" @click="confirmOrder(item)">确认收货</button> -->
							</view>
		
							<!--<text class="submit" @click="submit">提交订单</text> -->
						</view>
						
					</view>
					 
					<!-- <uni-load-more :status="tabItem.loadingType"></uni-load-more> -->
					
				</scroll-view>
			</swiper-item>
		</swiper>
		
		<!-- 取消订单原因面板-->
		<view class="mask" :class="maskState===0 ? 'none' : maskState===1 ? 'show' : ''" @click="cancelOrder('')">
			<view class="mask-content" @click.stop.prevent="stopPrevent">
				<view class="uni-list">
					<radio-group @change="radioChange">
						<label class="uni-list-cell uni-list-cell-pd" v-for="(item, index) in item1" :key="item.value">
							<view>
								<radio :value="item.value" :checked="index === current" />
							</view>
							<view>{{item.name}}</view>
						</label>
					</radio-group>	
					<button class="con-btn" @click="confirm">确认取消</button>	
					<button class="can-btn" @click="cancel">暂不取消</button>																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																																		
				</view>
			</view>
		</view>
	</view>
</template> 

<script>
	import uniLoadMore from '@/components/uni-load-more/uni-load-more.vue';
	import empty from "@/components/empty";
	import Json from '@/Json';
	export default {
		components: {
			uniLoadMore,
			empty
		},
		data() {
			return {
				item1: [{
						value: 'other1',
						name: '我不想买了',
						checked: 'true'
					},
					{
						value: 'other2',
						name: '卖家缺货'
					},
					{
						value: 'other3',
						name: '其他原因'
					},
					{
						value: 'other4',
						name: '信息填写错误重新拍',
					},
				],
				text1: "",
				text2: "",
				state1: "",
				addressData: {
					name: '许小星',
					mobile: '13853989563',
					addressName: '金九大道',
					address: '山东省济南市历城区',
					area: '149号',
					default: false,
				},
				tabCurrentIndex: 0,
				maskState: 0,
				navList: [{
						state: 0,
						text: '全部',
						loadingType: 'more',
						orderList: []
					},
					{
						state: 1,
						text: '待付款',
						loadingType: 'more',
						orderList: []
					},
					{
						state: 2,
						text: '待发货',
						loadingType: 'more',
						orderList: []
					},
					{
						state: 3,
						text: '待收货',
						loadingType: 'more',
						orderList: []
					},
					/* {
						state: 4,
						text: '售后',
						loadingType: 'more',
						orderList: []
					} */
				],
			};
		},
		
		onLoad(options){
			/**
			 * 修复app端点击除全部订单外的按钮进入时不加载数据的问题
			 * 替换onLoad下代码即可
			 */
			this.tabCurrentIndex = +options.state;
			this.state1=options.state;
			const that=this
			this.updateState(that,options.state)
			// #ifndef MP
			this.loadData()
			// #endif
			// #ifdef MP
			if(options.state == 0){
				this.loadData()
			}
			// #endif
			
		},
		 
		methods: {
			//获取订单列表
			loadData(source){
				//这里是将订单挂载到tab列表下
				let index = this.tabCurrentIndex;
				let navItem = this.navList[index];
				let state = navItem.state;
				console.log(state);
				if(source === 'tabChange' && navItem.loaded === true){
					//tab切换只有第一次需要加载数据
					return;
				}
				if(navItem.loadingType === 'loading'){
					//防止重复加载
					return;
				}
				
				navItem.loadingType = 'loading';
				
				setTimeout(()=>{
					let orderList = Json.orderList.filter(item=>{
						//添加不同状态下订单的表现形式
						item = Object.assign(item, this.orderStateExp(item.state));
						//演示数据所以自己进行状态筛选
						if(state === 0){
							//0为全部订单
							return item;
						}
						return item.state === state
					});
					orderList.forEach(item=>{
						navItem.orderList.push(item);
					})
					//loaded新字段用于表示数据加载完毕，如果为空可以显示空白页
					this.$set(navItem, 'loaded', true);
					
					//判断是否还有数据， 有改为 more， 没有改为noMore 
					navItem.loadingType = 'more';
				}, 600);	
			}, 
			stopTouchMove:function(e){
				return false;
			} ,
			//swiper 切换
			changeTab(e){
				this.tabCurrentIndex = e.target.current;
				this.loadData('tabChange');
			},
			//顶部tab点击
			tabClick(index){
				this.tabCurrentIndex = index;
			},
			//删除订单
			deleteOrder(index){
				uni.showLoading({
					title: '请稍后'
				})
				setTimeout(()=>{
					this.navList[this.tabCurrentIndex].orderList.splice(index, 1);
					uni.hideLoading();
				}, 600)
			},
			// //退款（退货退款）
			// returnItem(){
			// 	
			// },
			//取消订单
			cancelOrder(type){
				let timer = type === 'show' ? 10 : 300;
				let	state = type === 'show' ? 1 : 0;
				this.maskState = 1;
				setTimeout(()=>{
					this.maskState = state;
				}, timer)
				// uni.showLoading({
				// 	title: '请稍后'
				// })
				// setTimeout(()=>{
				// 	let {stateTip, stateTipColor} = this.orderStateExp(9);
				// 	item = Object.assign(item, {
				// 		state: 9,
				// 		stateTip, 
				// 		stateTipColor
				// 	})
				// 	
				// 	//取消订单后删除待付款中该项
				// 	let list = this.navList[1].orderList;
				// 	let index = list.findIndex(val=>val.id === item.id);
				// 	index !== -1 && list.splice(index, 1);
				// 	
				// 	uni.hideLoading();
				// }, 600)
			},
			//支付订单
			payOrder(item){
				uni.showLoading({
					title: '请稍后'
				})
				setTimeout(()=>{
					let {stateTip, stateTipColor} = this.orderStateExp(2);
					item = Object.assign(item, {
						state: 2,
						stateTip, 
						stateTipColor
					})
					
					//支付订单后删除待付款中该项
					let list = this.navList[1].orderList;
					let index = list.findIndex(val=>val.id === item.id);
					index !== -1 && list.splice(index, 1);
					
					uni.hideLoading();
				}, 600)
			},
			//申请开票
			applicate(){
				this.$api.msg('申请开票');
				uni.navigateTo({
					url: '/pages/receipt/openReceipt'
				});
			},
			//查看物流
			check(){
				this.$api.msg('查看物流');
				uni.navigateTo({
					url: '/pages/logistics/logistics'
				});
			},
			//退货
			returnItem(){
				this.$api.msg('退货');
				uni.navigateTo({
					url: '/pages/salesreturn/application'
				});
			},
			//确认收货
			confirmOrder(item){
				uni.showLoading({
					title: '请稍后'
				})
				setTimeout(()=>{
					let {stateTip, stateTipColor} = this.orderStateExp(8);
					item = Object.assign(item, {
						state: 8,
						stateTip, 
						stateTipColor
					})
					
					//收货后删除待收货中该项
					let list = this.navList[3].orderList;
					let index = list.findIndex(val=>val.id === item.id);
					index !== -1 && list.splice(index, 1);
					
					uni.hideLoading();
				}, 600)
			},
			radioChange(evt) {
				for (let i = 0; i < this.item1.length; i++) {
					if (this.item1[i].value === evt.target.value) {
						this.current = i;
						break;
					}
				}
			},
			stopPrevent(){},
			//确认取消订单
			confirm(){
				
			},
			//暂不取消订单
			cancel(){
				this.maskState=0;
			},
			//订单状态文字和颜色
			orderStateExp(state){
				let stateTip = '',
					stateTipColor = '#fa436a';
				switch(+state){
					case 1:
						stateTip = '等待买家付款'; break;
					case 2:
						stateTip = '等待卖家发货'; break;
					case 3:
						stateTip = '卖家已发货'; break;
					case 8:
						stateTip = '交易成功'; break;
					case 9:
						stateTip = '交易关闭'; 
						// stateTipColor = '#909399';
						break;
						
					//更多自定义
				}
				return {stateTip, stateTipColor};
			}
			,
			updateState(that,id){
				if(id==1){
					that.text1="等待买家付款"
					that.text2="还剩23小时49分45秒自动关闭"
				}else if(id==2){
					that.text1="等待卖家发货"
				}else if(id==3){
					that.text1="卖家已发货"
				}else if(id==8){
					that.text1="交易成功"
				}else if(id==9){
					that.text1="交易关闭"
				}
			}
		}
	}
</script>

<style lang="scss">
	page, .content{
		background: $page-color-base;
		height: 100%;
	}
	
	.swiper-box{
		height: calc(100% - 40px);
	}
	.list-scroll-content{
		height: 100%;
	}
	
	.address-section {
		padding: 30upx 0;
		background: #fff;
		position: relative;
	
		.order-content {
			display: flex;
			align-items: center;
		}
	
		.icon-shouhuodizhi {
			flex-shrink: 0;
			display: flex;
			align-items: center;
			justify-content: center;
			width: 90upx;
			color: #888;
			font-size: 44upx;
		}

		
		.footer{
			position: fixed;
			left: 0;
			bottom: 0;
			z-index: 995;
			display: flex;
			align-items: center;
			width: 100%;
			height: 90upx;
			justify-content: space-between;
			font-size: 30upx;
			background-color: #fff;
			z-index: 998;
			color: $font-color-base;
			box-shadow: 0 -1px 5px rgba(0,0,0,.1);
			.action-box{
				display: flex;
				justify-content: flex-end;
				align-items: center;
				height: 100upx;
				position: relative;
				padding-right: 30upx;
				.action-btn{
					width: 160upx;
						height: 60upx;
						margin: 0;
						margin-left: 150px;
						padding: 0;
						text-align: center;
						line-height: 60upx;
						font-size: $font-sm + 2upx;
						color: $font-color-dark;
						background: #fff;
						border-radius: 100px;
						&:after{
							border-radius: 100px;
						}
						&.recom{
							background: #fff9f9;
							color: $base-color;
							&:after{
								border-color: #f7bcc8;
							}
						}
					
				}
			}
		}
		.cen {
			display: flex;
			flex-direction: column;
			flex: 1;
			font-size: 28upx;
			color: $font-color-dark;
		}
	
		.name {
			font-size: 34upx;
			margin-right: 24upx;
		}
	
		.address {
			margin-top: 16upx;
			margin-right: 20upx;
			color: $font-color-light;
		}
	
		.icon-you {
			font-size: 32upx;
			color: $font-color-light;
			margin-right: 30upx;
		}
	
		.a-bg {
			position: absolute;
			left: 0;
			bottom: 0;
			display: block;
			width: 100%;
			height: 5upx;
		}
	}
	
	.mask{
		display: flex;
		align-items: flex-end;
		position: fixed;
		left: 0;
		top: var(--window-top);
		bottom: 0;
		width: 100%;
		background: rgba(0,0,0,0);
		z-index: 9995;
		transition: .3s;
		
		.mask-content{
			width: 100%;
			min-height: 30vh;
			max-height: 100vh;
			background: #f3f3f3;
			transform: translateY(100%);
			transition: .3s;
			overflow-y:scroll;
		}
		&.none{
			display: none;
		}
		&.show{
			background: rgba(0,0,0,.4);
			
			.mask-content{
				transform: translateY(0);
			}
		}
		.con-btn{
			display: flex;
			align-items: center;
			justify-content: center;
			width: 340upx;
			height: 80upx;
			margin: 60upx auto;
			font-size: $font-lg;
			color: #fff;
			background-color: $base-color;
			border-radius: 10upx;
			box-shadow: 1px 2px 5px rgba(219, 63, 96, 0.4);
		}
		.can-btn{
			display: flex;
			align-items: center;
			justify-content: center;
			width: 340upx;
			height: 80upx;
			margin: 60upx auto;
			font-size: $font-lg;
			color: #fff;
			background-color: $base-color;
			border-radius: 10upx;
			box-shadow: 1px 2px 5px rgba(219, 63, 96, 0.4);
		}
	}
	
	.navbar{
		display: flex;
		height: 40px;
		padding: 0 5px;
		background: #fff;
		box-shadow: 0 1px 5px rgba(0,0,0,.06);
		position: relative;
		z-index: 10;
		.nav-item{
			flex: 1;
			display: flex;
			justify-content: center;
			align-items: center;
			height: 100%;
			font-size: 15px;
			color: $font-color-dark;
			position: relative;
			&.current{
				color: $base-color;
				&:after{
					content: '';
					position: absolute;
					left: 50%;
					bottom: 0;
					transform: translateX(-50%);
					width: 44px;
					height: 0;
					border-bottom: 2px solid $base-color;
				}
			}
		}
	}

	.uni-swiper-item{
		height: auto;
	}
	.order-item{
		display: flex;
		flex-direction: column;
		padding-left: 30upx;
		background: #fff;
		margin-top: 16upx;
		.i-top{
			display: flex;
			align-items: center;
			height: 80upx;
			padding-right:30upx;
			font-size: $font-base;
			color: $font-color-dark;
			position: relative;
			.time{
				flex: 1;
			}
			.state{
				color: $base-color;
			}
			.del-btn{
				padding: 10upx 0 10upx 36upx;
				font-size: $font-lg;
				color: $font-color-light;
				position: relative;
				&:after{
					content: '';
					width: 0;
					height: 30upx;
					border-left: 1px solid $border-color-dark;
					position: absolute;
					left: 20upx;
					top: 50%;
					transform: translateY(-50%);
				}
			}
		}
		/* 多条商品 */
		.goods-box{
			height: 160upx;
			padding: 20upx 0;
			white-space: nowrap;
			.goods-item{
				width: 120upx;
				height: 120upx;
				display: inline-block;
				margin-right: 24upx;
				.right{
					flex: 1;
					display: flex;
					flex-direction: column;
					padding: 0 30upx 0 24upx;
					overflow: hidden;
					.title{
						font-size: $font-base + 2upx;
						color: $font-color-dark;
						line-height: 1;
					}
					.attr-box{
						font-size: $font-sm + 2upx;
						color: $font-color-light;
						padding: 10upx 12upx;
					}
					.price{
						font-size: $font-base + 2upx;
						color: $font-color-dark;
						&:before{
							content: '￥';
							font-size: $font-sm;
							margin: 0 2upx 0 8upx;
						}
					}
					.action-btn1{
						width: 160upx;
						height: 60upx;
						margin: 0;
						margin-left: 150px;
						padding: 0;
						text-align: center;
						line-height: 60upx;
						font-size: $font-sm + 2upx;
						color: $font-color-dark;
						background: #fff;
						border-radius: 100px;
						&:after{
							border-radius: 100px;
						}
						&.recom{
							background: #fff9f9;
							color: $base-color;
							&:after{
								border-color: #f7bcc8;
							}
						}
					}
				}
			}
			.goods-img{
				display: block;
				width: 100%;
				height: 100%;
			}
		}
		/* 单条商品 */
		.goods-box-single{
			display: flex;
			padding: 20upx 0;
			.goods-img{
				display: block;
				width: 120upx;
				height: 120upx;
			}
			.right{
				flex: 1;
				display: flex;
				flex-direction: column;
				padding: 0 30upx 0 24upx;
				overflow: hidden;
				.title{
					font-size: $font-base + 2upx;
					color: $font-color-dark;
					line-height: 1;
				}
				.attr-box{
					font-size: $font-sm + 2upx;
					color: $font-color-light;
					padding: 10upx 12upx;
				}
				.price{
					font-size: $font-base + 2upx;
					color: $font-color-dark;
					&:before{
						content: '￥';
						font-size: $font-sm;
						margin: 0 2upx 0 8upx;
					}
				}
				.action-btn1{
					width: 160upx;
					height: 60upx;
					margin: 0;
					margin-left: 150px;
					padding: 0;
					text-align: center;
					line-height: 60upx;
					font-size: $font-sm + 2upx;
					color: $font-color-dark;
					background: #fff;
					border-radius: 100px;
					&:after{
						border-radius: 100px;
					}
					&.recom{
						background: #fff9f9;
						color: $base-color;
						&:after{
							border-color: #f7bcc8;
						}
					}
				}
			}
			
		}
		
		.price-box{
			display: flex;
			justify-content: flex-end;
			align-items: baseline;
			padding: 20upx 30upx;
			font-size: $font-sm + 2upx;
			color: $font-color-light;
			.num{
				margin: 0 8upx;
				color: $font-color-dark;
			}
			.price{
				font-size: $font-lg;
				color: $font-color-dark;
				&:before{
					content: '￥';
					font-size: $font-sm;
					margin: 0 2upx 0 8upx;
				}
			}
		}
		
		
		.action-box{
			display: flex;
			justify-content: flex-end;
			align-items: center;
			height: 100upx;
			position: relative;
			padding-right: 30upx;
		}
		.action-btn{
			width: 160upx;
			height: 60upx;
			margin: 0;
			margin-left: 70upx;
			padding: 0;
			text-align: center;
			line-height: 60upx;
			font-size: $font-sm + 2upx;
			color: $font-color-dark;
			background: #fff;
			border-radius: 100px;
			&:after{
				border-radius: 100px;
			}
			&.recom{
				background: #fff9f9;
				color: $base-color;
				&:after{
					border-color: #f7bcc8;
				}
			}
		}
	}
	
	/* load-more */
	.uni-load-more {
		display: flex;
		flex-direction: row;
		height: 80upx;
		align-items: center;
		justify-content: center
	}
	
	.uni-load-more__text {
		font-size: 28upx;
		color: #999
	}
	
	.uni-load-more__img {
		height: 24px;
		width: 24px;
		margin-right: 10px
	}
	
	.uni-load-more__img>view {
		position: absolute
	}
	
	.uni-load-more__img>view view {
		width: 6px;
		height: 2px;
		border-top-left-radius: 1px;
		border-bottom-left-radius: 1px;
		background: #999;
		position: absolute;
		opacity: .2;
		transform-origin: 50%;
		animation: load 1.56s ease infinite
	}
	
	.uni-load-more__img>view view:nth-child(1) {
		transform: rotate(90deg);
		top: 2px;
		left: 9px
	}
	
	.uni-load-more__img>view view:nth-child(2) {
		transform: rotate(180deg);
		top: 11px;
		right: 0
	}
	
	.uni-load-more__img>view view:nth-child(3) {
		transform: rotate(270deg);
		bottom: 2px;
		left: 9px
	}
	
	.uni-load-more__img>view view:nth-child(4) {
		top: 11px;
		left: 0
	}
	
	.load1,
	.load2,
	.load3 {
		height: 24px;
		width: 24px
	}
	
	.load2 {
		transform: rotate(30deg)
	}
	
	.load3 {
		transform: rotate(60deg)
	}
	
	.load1 view:nth-child(1) {
		animation-delay: 0s
	}
	
	.load2 view:nth-child(1) {
		animation-delay: .13s
	}
	
	.load3 view:nth-child(1) {
		animation-delay: .26s
	}
	
	.load1 view:nth-child(2) {
		animation-delay: .39s
	}
	
	.load2 view:nth-child(2) {
		animation-delay: .52s
	}
	
	.load3 view:nth-child(2) {
		animation-delay: .65s
	}
	
	.load1 view:nth-child(3) {
		animation-delay: .78s
	}
	
	.load2 view:nth-child(3) {
		animation-delay: .91s
	}
	
	.load3 view:nth-child(3) {
		animation-delay: 1.04s
	}
	
	.load1 view:nth-child(4) {
		animation-delay: 1.17s
	}
	
	.load2 view:nth-child(4) {
		animation-delay: 1.3s
	}
	
	.load3 view:nth-child(4) {
		animation-delay: 1.43s
	}
	
	@-webkit-keyframes load {
		0% {
			opacity: 1
		}
	
		100% {
			opacity: .2
		}
	}
	.yt-list1 {
		margin-top: 16upx;
		background: #fff;
	}
	.yt-list-cell1 {
		display: flex;
		align-items: center;
		padding: 10upx 30upx 10upx 40upx;
		line-height: 70upx;
		position: relative;
	
		&.cell-hover {
			background: #fafafa;
		}
	
		&.b-b:after {
			left: 30upx;
		}
	
		.cell-icon {
			height: 32upx;
			width: 32upx;
			font-size: 22upx;
			color: #fff;
			text-align: center;
			line-height: 32upx;
			background: #f85e52;
			border-radius: 4upx;
			margin-right: 12upx;
	
			&.hb {
				background: #ffaa0e;
			}
	
			&.lpk {
				background: #3ab54a;
			}
	
		}
	
		.cell-more {
			align-self: center;
			font-size: 24upx;
			color: $font-color-light;
			margin-left: 8upx;
			margin-right: -10upx;
		}
	
		.cell-tit {
			flex: 1;
			font-size: 26upx;
			color: $font-color-light;
			margin-right: 10upx;
		}
	
		.cell-tip {
			font-size: 26upx;
			color: $font-color-dark;
	
			&.disabled {
				color: $font-color-light;
			}
	
			&.active {
				color: $base-color;
			}
			&.red{
				color: $base-color;
			}
		}
	
		&.desc-cell {
			.cell-tit {
				max-width: 110upx;
			}
		}
	
		.desc {
			flex: 1;
			font-size: $font-base;
			color: $font-color-dark;
		}
	}
	.mym{
		padding: 5upx 0;
		background: #fafafa;
		position: relative;
		margin-left: -20px;
	}
</style>
