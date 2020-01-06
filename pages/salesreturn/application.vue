<template>
	<view>
		<view class="goods-section">
			<!-- 商品列表 -->

			<view class="g-item">
				<image src="https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=1620020012,789258862&fm=26&gp=0.jpg"></image>
				<view class="right">
					<text class="title clamp">韩版于是洞洞拖鞋 夏季浴室防滑简约居家【新人专享，限选意见】</text>
					<text class="spec">春装款 L</text>
					<view class="price-box">
						<text class="price">￥17.8</text>
						<text class="number">x 1</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 优惠明细 -->
		<view class="yt-list">
			<!-- <view class="yt-list-cell b-b" @click="toggleMask('show')">
				<text class="cell-tit clamp">退款原因</text>
				<text class="cell-tip active">
					请选择退款原因
				</text>
				<text class="cell-more wanjia wanjia-gengduo-d"></text>
			</view> -->
			<view class="yt-list-cell b-b">
				<text class="cell-tit clamp">退款原因</text>
				<view class="cell-tip active">
					<picker @change="bindPickerChange" :value="index" :range="returnReason" range-key="name">
						<view class="uni-input">{{returnReason[index].name}}</view>
					</picker>
				</view>
			</view>
			<view class="yt-list-cell b-b">
				<text class="cell-tit clamp">退款数量</text>
				<text class="cell-tip disabled">1件</text>
			</view>
		</view>
		<view style="font-size: .4em; margin:10upx 40upx;">最多可退10件，每件可退￥23.8</view>
		<!-- 金额明细 -->
		<view class="yt-list" >
			<view class="yt-list-cell b-b">
				<text class="cell-tit clamp">运费</text>
				<text class="cell-tip">￥17</text>
			</view>
			<view class="yt-list-cell b-b">
				<text class="cell-tit clamp">退款金额</text>
				<text class="cell-tip clamp">￥35</text>
			</view>
			<view class="yt-list-cell b-b">
				<text class="cell-tit clamp">手机号码</text>
				<input class="cell-tip clamp" style="text-align: right;" type="text"/>
			</view>
			<!-- <view class="border"> -->
			<view class="dces-area  b-b">
				<text class="cell-tit clamp">退款说明</text>
				<textarea class="desc" placeholder="选填,最多200字" auto-height></textarea>
				
			</view>
			<!-- <view class="border"/> -->
			<view class="yt-list-cell">
				<text class="cell-tit clamp">上传凭证</text>
				<text class="cell-tip disabled">最多可上传5张图片</text>
			</view>
			<view class="yt-list-cell b-b">
				<view class="uni-uploader">
					<view class="uni-uploader-head">
						<view class="uni-uploader-title">点击可预览选好的图片</view>
						<view class="uni-uploader-info">{{imageList.length}}/5</view>
					</view>
					<view class="uni-uploader-body">
						<view class="uni-uploader__files">
							<block v-for="(image,index) in imageList" :key="index">
								<view class="uni-uploader__file">
									<image class="uni-uploader__img" :src="image" :data-src="image" @tap="previewImage"></image>
								</view>
							</block>
							<view class="uni-uploader__input-box">
								<view class="uni-uploader__input" @tap="chooseImage"></view>
							</view>
						</view>
					</view>
				</view>
			</view>
			
		</view>
		<button class="confirm-btn" @click="applicationReturnGoods" :disabled="logining">提交</button>
	</view>
</template>

<script>
	// #ifdef APP-PLUS
	import permision from "common/permission.js"
	// #endif
	var sourceType = [
	    ['camera'],
	    ['album'],
	    ['camera', 'album']
	]
	var sizeType = [
	    ['compressed'],
	    ['original'],
	    ['compressed', 'original']
	]
	export default {
		data() {
			return {
				imageList: [],
				sourceTypeIndex: 2,
				sourceType: ['拍照', '相册', '拍照或相册'],
				sizeTypeIndex: 2,
				sizeType: ['压缩', '原图', '压缩或原图'],
				countIndex: 4,
				count: [1, 2, 3, 4, 5],
				index:1,
				returnReason: [{name:'未按约定时间发货'},{name: '拍错/多拍/不喜欢'}, {name:'协商一致退款'}, {name:'其他'}],
				maskState: 0, //优惠券面板显示状态
				desc: '', //备注
				payType: 1, //1微信 2支付宝
				returnGoods: {},
				usrPrnOrderNo: '',
				productSku: '',
				logining: true
			}
		},
		onUnload() {
		    this.imageList = [],
			this.sourceTypeIndex = 2,
			this.sourceType = ['拍照', '相册', '拍照或相册'],
			this.sizeTypeIndex = 2,
			this.sizeType = ['压缩', '原图', '压缩或原图'],
			this.countIndex = 4;
		},
		onLoad(option){
			//获取商品详情
			this.getReturnGoodsInfo();
			this.usrPrnOrderNo = '002';
			this.productSku = '002';
		},
		methods: {
			bindPickerChange: function(e) {
				console.log('picker发送选择改变，携带值为：' + e.target.value)
				this.index = e.target.value
			},
			previewImage: function(e) {
			    var current = e.target.dataset.src
			    uni.previewImage({
			        current: current,
			        urls: this.imageList
			    })
			},
			sourceTypeChange: function(e) {
			    this.sourceTypeIndex = e.target.value
			},
			sizeTypeChange: function(e) {
			    this.sizeTypeIndex = e.target.value
			},
			countChange: function(e) {
			    this.countIndex = e.target.value;
			},
			chooseImage: async function() {
			    // #ifdef APP-PLUS
			    // TODO 选择相机或相册时 需要弹出actionsheet，目前无法获得是相机还是相册，在失败回调中处理
			    if (this.sourceTypeIndex !== 2) {
			        let status = await this.checkPermission();
			        if (status !== 1) {
			            return;
			        }
			    }
			    // #endif
			
			    if (this.imageList.length === 5) {
			        let isContinue = await this.isFullImg();
			        console.log("是否继续?", isContinue);
			        if (!isContinue) {
			            return;
			        }
			    }
			    uni.chooseImage({
			        sourceType: sourceType[this.sourceTypeIndex],
			        sizeType: sizeType[this.sizeTypeIndex],
			        count: this.imageList.length + this.count[this.countIndex] > 5 ? 5 - this.imageList.length :
			            this.count[this.countIndex],
			        success: (res) => {
			            this.imageList = this.imageList.concat(res.tempFilePaths);
			        },
			        fail: (err) => {
			            // #ifdef APP-PLUS
			            if (err['code'] && err.code !== 0 && this.sourceTypeIndex === 2) {
			                this.checkPermission(err.code);
			            }
			            // #endif
			        }
			    })
			},
			isFullImg: function() {
			    return new Promise((res) => {
			        uni.showModal({
			            content: "已经有5张图片了,是否清空现有图片？",
			            success: (e) => {
			                if (e.confirm) {
			                    this.imageList = [];
			                    res(true);
			                } else {
			                    res(false)
			                }
			            },
			            fail: () => {
			                res(false)
			            }
			        })
			    })
			},
			async checkPermission(code) {
			    let type = code ? code - 1 : this.sourceTypeIndex;
			    let status = permision.isIOS ? await permision.requestIOS(sourceType[type][0]) :
			        await permision.requestAndroid(type === 0 ? 'android.permission.CAMERA' :
			            'android.permission.READ_EXTERNAL_STORAGE');
			
			    if (status === null || status === 1) {
			        status = 1;
			    } else {
			        uni.showModal({
			            content: "没有开启权限",
			            confirmText: "设置",
			            success: function(res) {
			                if (res.confirm) {
			                    permision.gotoAppSetting();
			                }
			            }
			        })
			    }
			
			    return status;
			},
			//显示优惠券面板
			toggleMask(type){
				let timer = type === 'show' ? 10 : 300;
				let	state = type === 'show' ? 1 : 0;
				this.maskState = 2;
				setTimeout(()=>{
					this.maskState = state;
				}, timer)
			},
			numberChange(data) {
				this.number = data.number;
			},
			changePayType(type){
				this.payType = type;
			},
			submit(){
				uni.redirectTo({
					url: '/pages/order/detail'
				})
			},
			stopPrevent(){},
			async getReturnGoodsInfo() {
				this.logining = true;
				this.showLoading();
				console.log("getReturnGoodsInfo");
				
				const usrPrnOrderNo = '002';
				const productSku = '002';
				
				// console.log("mobile:"+mobile);
				// console.log("password:"+password);
			
				const [err, res] = await uni.request({
					url: this.$userCenter+"api/v1/order/returnGoodsInfo",
					method: 'GET',
					header: {
						'content-type': 'application/x-www-form-urlencoded',
					},
					data: {
						usrPrnOrderNo: usrPrnOrderNo,
						productSku: productSku
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
					this.returnGoods = res.data;
				} else {
					this.$api.msg(result.msg);
					this.logining = false;
				}
			},
			async applicationReturnGoods() {
				this.logining = true;
				this.showLoading();
				console.log("applicationReturnGoods");
				
				const usrNo = '12040549';
				const usrPrnOrderNo = returnGoods.usrPrnOrderNo;
				const productSku = returnGoods.productSku;
				const returningQuantity = returnGoods.returningQuantity;
				const returningFreight = returnGoods.freight;
				const returningAmount = returnGoods.productAmount;
				const retRsn = '没有原因';
				const returningRemark = '没有原因';
				const usrPhone = '13150029323';
				const usrRetVchr = '';
				
				// console.log("mobile:"+mobile);
				// console.log("password:"+password);
			
				const [err, res] = await uni.request({
					url: this.$userCenter+"api/v1/order/uiUsrRet",
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded',
					},
					data: {
						usrNo: usrNo,
						usrPrnOrderNo: usrPrnOrderNo,
						productSku: productSku,
						returningQuantity: returningQuantity,
						returningFreight: returningFreight,
						returningAmount: returningAmount,
						retRsn: retRsn,
						returningRemark: returningRemark,
						usrPhone: usrPhone,
						usrRetVchr: usrRetVchr
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
					this.returnGoods = res.data;
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
		}
	}
</script>

<style lang="scss">
	page {
		background: $page-color-base;
		padding-bottom: 100upx;
	}
	.confirm-btn{
		width: 350upx;
		height: 76upx;
		line-height: 76upx;
		border-radius: 50px;
		margin-top: 70upx;
		background: #0A98D5;
		color: #fff;
		font-size: $font-lg;
		&:after{
			border-radius: 100px;
		}
	}
	/* 上传 */
	.uni-uploader {
		flex: 1;
		flex-direction: column;
	}
	.uni-uploader-head {
		display: flex;
		flex-direction: row;
		justify-content: space-between;
	}
	.uni-uploader-title {
		font-size: 26upx;
	}
	.uni-uploader-info {
		font-size: 26upx;
		color: #B2B2B2;
	}
	.uni-uploader-body {
		margin-top: 16upx;
	}
	.uni-uploader__files {
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
	}
	.uni-uploader__file {
		margin: 10upx;
		width: 210upx;
		height: 210upx;
	}
	.uni-uploader__img {
		display: block;
		width: 210upx;
		height: 210upx;
	}
	.uni-uploader__input-box {
		position: relative;
		margin:10upx;
		width: 208upx;
		height: 208upx;
		border: 2upx solid #D9D9D9;
	}
	.uni-uploader__input-box:before,
	.uni-uploader__input-box:after {
		content: " ";
		position: absolute;
		top: 50%;
		left: 50%;
		-webkit-transform: translate(-50%, -50%);
		transform: translate(-50%, -50%);
		background-color: #D9D9D9;
	}
	.uni-uploader__input-box:before {
		width: 4upx;
		height: 79upx;
	}
	.uni-uploader__input-box:after {
		width: 79upx;
		height: 4upx;
	}
	.uni-uploader__input-box:active {
		border-color: #999999;
	}
	.uni-uploader__input-box:active:before,
	.uni-uploader__input-box:active:after {
		background-color: #999999;
	}
	.uni-uploader__input {
		position: absolute;
		z-index: 1;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		opacity: 0;
	}
	
	
	
	.list-pd {
	    margin-top: 50upx;
	}
	.cell-pd {
	    padding: 22upx 30upx;
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

	.goods-section {
		margin-top: 16upx;
		background: #fff;
		padding-bottom: 1px;

		.g-header {
			display: flex;
			align-items: center;
			height: 84upx;
			padding: 0 30upx;
			position: relative;
		}

		.logo {
			display: block;
			width: 50upx;
			height: 50upx;
			border-radius: 100px;
		}

		.name {
			font-size: 30upx;
			color: $font-color-base;
			margin-left: 24upx;
		}

		.g-item {
			display: flex;
			margin: 20upx 30upx;
			padding: 30upx;
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
	.yt-list {
		margin-top: 16upx;
		background: #fff;
	}

	.yt-list-cell {
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
	
	.dces-area {
		display: flex;
		align-items: center;
		justify-content: flex-start;
		flex-direction: column;
		padding: 10upx 30upx 10upx 40upx;
		line-height: 70upx;
		position: relative;
	
		&.cell-hover {
			background: #fafafa;
		}
	
		&.b-b:after {
			left: 30upx;
		}
	
		.cell-tit {
			width: 100%;
			font-size: 26upx;
			color: $font-color-light;
			margin-right: 10upx;
		}
		.desc {
			width: 100%;
			font-size: $font-base;
			color: $font-color-dark;
		}
	}
	
	/* 支付列表 */
	.pay-list{
		padding-left: 40upx;
		margin-top: 16upx;
		background: #fff;
		.pay-item{
			display: flex;
			align-items: center;
			padding-right: 20upx;
			line-height: 1;
			height: 110upx;	
			position: relative;
		}
		.icon-weixinzhifu{
			width: 80upx;
			font-size: 40upx;
			color: #6BCC03;
		}
		.icon-alipay{
			width: 80upx;
			font-size: 40upx;
			color: #06B4FD;
		}
		.icon-xuanzhong2{
			display: flex;
			align-items: center;
			justify-content: center;
			width: 60upx;
			height: 60upx;
			font-size: 40upx;
			color: $base-color;
		}
		.tit{
			font-size: 32upx;
			color: $font-color-dark;
			flex: 1;
		}
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
		.price-content{
			padding-left: 30upx;
		}
		.price-tip{
			color: $base-color;
			margin-left: 8upx;
		}
		.price{
			font-size: 36upx;
			color: $base-color;
		}
		.submit{
			display:flex;
			align-items:center;
			justify-content: center;
			width: 280upx;
			height: 100%;
			color: #fff;
			font-size: 32upx;
			background-color: $base-color;
		}
	}
	
	/* 优惠券面板 */
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
			max-height: 70vh;
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
	}

	/* 优惠券列表 */
	.coupon-item{
		display: flex;
		flex-direction: column;
		margin: 20upx 24upx;
		background: #fff;
		.con{
			display: flex;
			align-items: center;
			position: relative;
			height: 120upx;
			padding: 0 30upx;
			&:after{
				position: absolute;
				left: 0;
				bottom: 0;
				content: '';
				width: 100%;
				height: 0;
				border-bottom: 1px dashed #f3f3f3;
				transform: scaleY(50%);
			}
		}
		.left{
			display: flex;
			flex-direction: column;
			justify-content: center;
			flex: 1;
			overflow: hidden;
			height: 100upx;
		}
		.title{
			font-size: 32upx;
			color: $font-color-dark;
			margin-bottom: 10upx;
		}
		.time{
			font-size: 24upx;
			color: $font-color-light;
		}
		.right{
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;
			font-size: 26upx;
			color: $font-color-base;
			height: 100upx;
		}
		.price{
			font-size: 44upx;
			color: $base-color;
			&:before{
				content: '￥';
				font-size: 34upx;
			}
		}
		.tips{
			font-size: 24upx;
			color: $font-color-light;
			line-height: 60upx;
			padding-left: 30upx;
		}
		.circle{
			position: absolute;
			left: -6upx;
			bottom: -10upx;
			z-index: 10;
			width: 20upx;
			height: 20upx;
			background: #f3f3f3;
			border-radius: 100px;
			&.r{
				left: auto;
				right: -6upx;
			}
		}
	}
	
	.border{
		width: 100%;
		height: 2upx;
		background: #c8c7cc;
	}

</style>
