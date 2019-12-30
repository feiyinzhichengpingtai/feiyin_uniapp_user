<template>
	<view class="content">
		<view class="row default-row">
			<text class="tit">发票类型</text>
			<!-- <input class="input" type="text" v-model="addressData.name" placeholder="收货人姓名" placeholder-class="placeholder" /> -->
			<xfl-select
			class="input"
			:list="list"
			:initValue="'电子发票'"
			:showItemNum="2" 
			:isCanInput="false"  
			:placeholder = "'请选择发票类型'"
			@visible-change = 'visibleChange'
			>
			<!-- :style_Container="listBoxStyle" -->
			</xfl-select>
		</view>
		<view class="row default-row">
			<text class="tit">抬头类型</text>
			<xfl-select
			class="input"
			:list="list1"
			:initValue="'个人或事业单位'"
			:showItemNum="2" 
			:isCanInput="false"  
			:placeholder = "'请选择抬头类型'"
			@visible-change = 'visibleChange'
			>
			<!-- :style_Container="listBoxStyle" -->
			</xfl-select>
			
		</view>
		<view class="row default-row">
			<text class="tit">发票抬头</text>
			<input class="input" type="text" v-model="addressData.paperTitle" placeholder="上海xxx公司" placeholder-class="placeholder" />
		</view>
		<view class="row default-row"> 
			<text class="tit">纳税人编号</text>
			<input class="input" type="text" v-model="addressData.TaxPayerId" placeholder="4423422122Y" placeholder-class="placeholder" />
		</view>
		
		<view class="row default-row">
			<text class="tit">开户银行</text>
			<input class="input" type="text" v-model="addressData.mobile" placeholder="选填" placeholder-class="placeholder" />
		</view>
		<view class="row default-row">
			<text class="tit">银行账号</text>
			<input class="input" type="text" v-model="addressData.mobile" placeholder="选填" placeholder-class="placeholder" />
		</view>
		<view class="row default-row">
			<text class="tit">企业地址</text>
			<input class="input" type="text" v-model="addressData.mobile" placeholder="选填" placeholder-class="placeholder" />
		</view>
		<view class="row default-row">
			<text class="tit">企业电话</text>
			<input class="input" type="number" v-model="addressData.mobile" placeholder="选填" placeholder-class="placeholder" />
		</view>
		<view class="row default-row">
			<text class="tit">设为默认发票信息</text>
			<switch :checked="addressData.defaule" color="#fa436a" @change="switchChange" />
		</view>
		<view class="uni-btn-v">
			<navigator url="../../pages/order/createOrder" hover-class="navigator-hover">
				<button type="default">本次不开具发票，继续下单&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;></button>
			</navigator>
		</view>
		<button class="add-btn" @click="confirm">完成</button>
	</view>
</template>

<script>
	import xflSelect from 'components/xfl-select/xfl-select.vue';
	export default {
		data() {
			return {
				/* listBoxStyle: `height: 40px; font-size: 16px;`, */
				list: [
					'电子发票',
					'纸质发票',
				],
				list1: [
					'个人或事业单位',
					'企业',
				],
			}
		},
		/* onLoad(option){
			let title = '开具发票';
			if(option.type==='edit'){
				title = '编辑收货地址'
				
				this.addressData = JSON.parse(option.data)
			}
			this.manageType = option.type;
			uni.setNavigationBarTitle({
				title
			})
		}, */
		back() {
			uni.navigateBack({
				delta: 1
			})
		},
		methods: {
			switchChange(e){
				this.addressData.default = e.detail;
			},
			
			//地图选择地址
			chooseLocation(){
				uni.chooseLocation({
					success: (data)=> {
						this.addressData.addressName = data.name;
						this.addressData.address = data.name;
					}
				})
			},
			
			//提交
			confirm(){
				let data = this.addressData;
				if(!data.paperTitle){
					this.$api.msg('请填写发票抬头');
					return;
				}
				if(!data.TaxPayerId){
					this.$api.msg('请填写纳税人编号');
					return;
				}
				
				//this.$api.prePage()获取上一页实例，可直接调用上页所有数据和方法，在App.vue定义
				this.$api.prePage().refreshList(data, this.manageType);
				this.$api.msg(`地址${this.manageType=='edit' ? '修改': '添加'}成功`);
				setTimeout(()=>{
					uni.navigateBack()
				}, 800)
			},
		},
		components: { xflSelect },
	}
</script>

<style lang="scss">
	page{
		background: $page-color-base;
		padding-top: 16upx;
	}
	.title {
		border-top: 1px #f5f5f5 solid;
		padding: 30upx;
		background: #fff
	}
	.row{
		display: flex;
		align-items: center;
		position: relative;
		padding:0 30upx;
		height: 110upx;
		background: #fff;
		
		.tit{
			flex-shrink: 0;
			width: 120upx;
			font-size: 30upx;
			color: $font-color-dark;
		}
		.input{
			flex: 1;
			font-size: 30upx;
			color: $font-color-dark;
		}
		.icon-shouhuodizhi{
			font-size: 36upx;
			color: $font-color-light;
		}
	}
	.default-row{
		margin-top: 16upx;
		.tit{
			flex: 1;
		}
		switch{
			transform: translateX(16upx) scale(.9);
		}
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
