<template>
	<view class="content">
		<view class="navbar" :style="{position:headerPosition,top:headerTop}">
			<view class="nav-item" :class="{current: filterIndex === 0}" @click="tabClick(0)">
				综合排序
			</view>
			<view class="nav-item" :class="{current: filterIndex === 1}" @click="tabClick(1)">
				销量优先
			</view>
			<view class="nav-item" :class="{current: filterIndex === 2}" @click="tabClick(2)">
				<text>价格</text>
				<view class="p-box">
					<text :class="{active: priceOrder === 1 && filterIndex === 2}" class="yticon icon-shang"></text>
					<text :class="{active: priceOrder === 2 && filterIndex === 2}" class="yticon icon-shang xia"></text>
				</view>
			</view>
			<text class="cate-item yticon icon-fenlei1" @click="toggleCateMask('show')"></text>
		</view>
		<view class="goods-list">
			<view style="width: 100%;" v-for="(item, index) in goodsList" :key="index" @click="navToDetailPage(item)">
				<view class="g-item" >
						<image :src="item.spuCmdtyNo"></image>
						<view class="right">
							<text class="title clamp">{{item.spuCmdtyNm}}</text>
							<text class="spec">春装款 L</text>
							<view class="price-box">
								<text class="price">{{item.spuCmdtyLowstPrc}}</text>
								<text class="number">已售 {{item.monthSaleQty}}</text>
							</view>
						</view>
				</view>
				<view class="border"></view>
			</view>
		</view>
		<uni-load-more :status="loadingType" ></uni-load-more>
		<view class="cate-mask" :class="cateMaskState===0 ? 'none' : cateMaskState===1 ? 'show' : ''" @click="toggleCateMask">
			<view class="cate-content" @click.stop.prevent="stopPrevent" @touchmove.stop.prevent="stopPrevent">
				<view class="cate-title">筛选</view>
				<view class="border"/>
				<view class="cat-content-center"> 
					<view class="cat-content-center-price">
						<view class="text" style="width: 200upx;">价格</view>
						<view class="text" style="-webkit-flex: 1;flex: 1;display: flex;flex-direction: row; align-items: center;">
							<input style="display: inline-block;" type="number" placeholder="低价"  v-model="bottom_price" placeholder-style="font-size: 26upx;"/>
							&nbsp;&nbsp;-&nbsp;&nbsp;
							<input style="display: inline-block;" type="number" placeholder="高价" v-model="top_price" placeholder-style="font-size: 26upx;">
						</view>
					</view>
					<view class="cat-content-center-price">
						<view class="text" style="width: 200upx;">地区</view>
						<xfl-select
							style="-webkit-flex: 1;flex: 1; font-size: 24upx; margin: 0 8upx;"
							:list="list"
							:initValue="'无'"
							:showItemNum="2" 
							:isCanInput="false"  
							:placeholder = "'地区'"
							@change="selectChange">
						</xfl-select>
					</view>
					<view class="cat-content-center-button">
						<button type="primary" @click="toggleCateMask()" style="width: 200upx;">确定</button>
						<button type="default" plain="true" @click="toggleCateMask('clear')" style="width: 200upx;">清除</button>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import uniLoadMore from '@/components/uni-load-more/uni-load-more.vue';
	import xflSelect from 'components/xfl-select/xfl-select.vue';
	export default {
		components: {
			uniLoadMore	,
			xflSelect
		},
		
		data() {
			return {
				cateMaskState: 0, //分类面板展开状态
				headerPosition:"fixed",
				headerTop:"0px",
				loadingType: 'more', //加载更多状态
				cateId: 0, //已选三级分类id
				keyword: '',
				page: 0,
				pageSize: 20,
				orderBy: '',
				region: '',
				bottom_price: '',
				top_price: '',
				filterIndex: 0, 
				priceOrder: 0, //1 价格从低到高 2价格从高到低
				cateList: [],
				goodsList: [],
				list: [
					'西安',
					'成都',
				]
			};
		},
		
		onLoad(options){
			// #ifdef H5
			this.headerTop = document.getElementsByTagName('uni-page-head')[0].offsetHeight+'px';
			// #endif
			this.cateId = options.tid;
			this.keyword = "iphone";
			this.page = 1;
			this.pageSize = 10;
			this.total = 0;  // 总商品数
			this.pages = 0; // 总页数
			this.loadData();
		},
		onPageScroll(e){
			//兼容iOS端下拉时顶部漂移
			if(e.scrollTop>=0){
				this.headerPosition = "fixed";
			}else{
				this.headerPosition = "absolute";
			}
		},
		//下拉刷新
		onPullDownRefresh(){
			this.loadData('refresh');
		},
		//加载更多
		onReachBottom(){
			console.log("onReachBottom");
			this.loadData();
		},
		methods: {
			//加载商品 ，带下拉刷新和上滑加载
			async loadData(type='add', loading) {
				//没有更多直接返回
				if(type === 'add'){
					if(this.loadingType === 'nomore'){
						return;
					}
					this.loadingType = 'loading';
				}else{
					this.loadingType = 'more'
				}
				
				//let goodsList = await this.$api.json('goodsList');
				if(type === 'refresh'){
					this.goodsList = [];
				}
				
				//网络请求开始
				this.showLoading();
				console.log("sales list");

				const bottom_price = this.bottom_price;
				const top_price = this.top_price;
				const region = this.region;
				let order = ""
				if(this.filterIndex === 1){
					order = "monthSaleQty desc" ;
				}else if(this.filterIndex === 2){
					order = this.priceOrder === 1 ? 'priceOrder asc' : 'priceOrder desc';
				}else{
					order = "";
				}
				const orderBy = order;
				const keyword = this.keyword;
				const page = this.page;
				
				const [err, res] = await uni.request({
					url: this.$userCenter+"api/v1/goods/goodsList",
					method: 'GET',
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						'deviceNo': '121',
						'channelNo': '2202',
						'systemNo': '0657'
					},
					data: {
						keyword: keyword,
						orderBy: orderBy,
						region: region,
						top_price: top_price,
						bottom_price: bottom_price,
						page: page,
						pageSize: this.pageSize
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
					let data = res.data;
					this.goodsList = this.goodsList.concat(data.list);
					this.page = this.page;
					this.total = res.data.total;
					this.pages = res.data.pages;
					//判断是否还有下一页，有是more  没有是nomore(测试数据判断大于20就没有了)
					this.loadingType  = this.pages === page ? 'nomore' : 'more';
					if(type === 'refresh'){
						if(loading == 1){
							uni.hideLoading();
						}else{
							uni.stopPullDownRefresh();
						}
					}
					
				} else {
					this.$api.msg(result.msg);
					this.logining = false;
				}
			},
			//筛选点击
			tabClick(index){
				if(this.filterIndex === index && index !== 2){
					return;
				}
				this.filterIndex = index;
				if(index === 2){
					this.priceOrder = this.priceOrder === 1 ? 2: 1;
				}else{
					this.priceOrder = 0; //不按照价格排序
				}
				uni.pageScrollTo({
					duration: 300,
					scrollTop: 0
				})
				this.loadData('refresh', 1);
				uni.showLoading({
					title: '正在加载'
				})
			},
			//显示分类面板
			toggleCateMask(type){
				let timer = type === 'show' ? 10 : 300;
				let	state = type === 'show' ? 1 : 0;
				this.cateMaskState = 2;
				
				if(type === 'clear'){
					this.region = '';
					this.bottom_price = '';
					this.top_price = '';
				}
				
				setTimeout(()=>{
					this.cateMaskState = state;
				}, timer)
			},
			
			//详情
			navToDetailPage(item){
				//测试数据没有写id，用title代替
				let id = item.title;
				console.log("id:"+id);
				uni.navigateTo({
					url: `/pages/product/product?id=${id}`
				})
			},
			stopPrevent(){},
			visibleChange(isShow){
				console.log("isShow:"+isShow);
			},
			selectChange(data){
				this.region = data.newVal;
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

<style lang="scss">
	page, .content{
		background: $page-color-base;
	}
	.content{
		padding-top: 96upx;
	}

	.navbar{
		position: relative;
		left: 0;
		top: 100upx;
		display: flex;
		width: 100%;
		height: 80upx;
		background: #fff;
		box-shadow: 0 2upx 10upx rgba(0,0,0,.06);
		z-index: 10;
		.titleNview-placing {
			height: 100upx;
			padding-top: 44px;
			box-sizing: content-box;
		}
		.titleNview-background {
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 426upx;
			transition: .4s;
		}
		.nav-item{
			flex: 1;
			display: flex;
			justify-content: center;
			align-items: center;
			height: 100%;
			font-size: 30upx;
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
					width: 120upx;
					height: 0;
					border-bottom: 4upx solid $base-color;
				}
			}
		}
		.p-box{
			display: flex;
			flex-direction: column;
			.yticon{
				display: flex;
				align-items: center;
				justify-content: center;
				width: 30upx;
				height: 14upx;
				line-height: 1;
				margin-left: 4upx;
				font-size: 26upx;
				color: #888;
				&.active{
					color: $base-color;
				}
			}
			.xia{
				transform: scaleY(-1);
			}
		}
		.cate-item{
			display: flex;
			justify-content: center;
			align-items: center;
			height: 100%;
			width: 80upx;
			position: relative;
			font-size: 44upx;
			&:after{
				content: '';
				position: absolute;
				left: 0;
				top: 50%;
				transform: translateY(-50%);
				border-left: 1px solid #ddd;
				width: 0;
				height: 36upx;
			}
		}
	}

	/* 分类 */
	.cate-mask{
		position: fixed;
		left: 0;
		top: var(--window-top);
		bottom: 0;
		width: 100%;
		background: rgba(0,0,0,0);
		z-index: 95;
		transition: .3s;
		display: flex;
		align-items: center;
		justify-content: center;
		.cate-content{
			width: 630upx;
			height: 40%;
			background: #fff;
			display: flex;
			flex-direction: column;
			transform: translateX(100%);
			transition: .3s;
			.cate-title{
				display: flex;
				padding: 30upx 30upx;
				font-size: 40upx;
				font-weight: bold ;
				margin-left: 20upx;
			}
			.cat-content-center{
				display: flex;
				flex-direction: column;
			}
			.cat-content-center-price{
				display: flex;
				flex-direction: row;
				justify-content: center;
				align-items: center;
				margin: 0 20upx;
			}
			.cat-content-center-button{
				display: flex;
				padding: 30upx 0;
				flex-direction: row;
				justify-content: space-around;
			}
		}
		&.none{
			display: none;
		}
		&.show{
			background: rgba(0,0,0,.4);
			
			.cate-content{
				transform: translateX(0);
			}
		}
	}
	.cate-list{
		display: flex;
		flex-direction: column;
		height: 100%;
		.cate-item{
			display: flex;
			align-items: center;
			height: 90upx;
			padding-left: 30upx;
 			font-size: 28upx;
			color: #555;
			position: relative;
		}
		.two{
			height: 64upx;
			color: #303133;
			font-size: 30upx;
			background: #f8f8f8;
		}
		.active{
			color: $base-color;
		}
	}

	/* 商品列表 */
	.goods-list{
		display:flex;
		flex-wrap:wrap;
		padding: 0 0upx;
		background: #fff;
		.g-item {
			display: flex;
			margin: 20upx 30upx;
			padding: 0 25upx;
			width: 100%;
			image {
				flex-shrink: 0;
				display: block;
				width: 140upx;
				height: 140upx;
				border-radius: 4upx;
			}
		
			.right {
				flex: 1;
				padding-left: 24upx;
				overflow: hidden;
			}
		
			.title {
				font-size: 30upx;
				color: $font-color-dark;
			}
		
			.spec {
				font-size: 26upx;
				color: $font-color-light;
			}
		
			.price-box {
				display: flex;
				align-items: center;
				font-size: 32upx;
				color: $font-color-dark;
				padding-top: 10upx;
		
				.price {
					margin-bottom: 4upx;
				}
				.number{
					font-size: 26upx;
					color: $font-color-base;
					margin-left: 20upx;
				}
			}
		
			.step-box {
				position: relative;
			}
		}
	}
	.border{
		height: 2upx;
		width: 100%;
		background: #fafafa;
	}
	.text {
		margin: 15upx 10upx;
		padding: 0 20upx;
		background-color: #ebebeb;
		height: 70upx;
		line-height: 70upx;
		text-align: center;
		color: #777;
		font-size: 26upx;
	}

</style>
