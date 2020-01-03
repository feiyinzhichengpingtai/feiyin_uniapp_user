<template>
	<view class="content">
		<view class="row default-row">
			<text class="tit">发票类型</text>
			<!-- <input class="input" type="text" v-model="addressData.name" placeholder="收货人姓名" placeholder-class="placeholder" /> -->
			<xfl-select
			class="input"
			:list="list"
			:initValue="receipt.paperType"
			:showItemNum="2" 
			:isCanInput="false"  
			:placeholder = "'请选择发票类型'"
			@change="ischange"
			>
			<!-- :style_Container="listBoxStyle" -->
			</xfl-select>
		</view>
		<view class="row default-row">
			<text class="tit">抬头类型</text>
			<xfl-select
			class="input"
			:list="list1"
			:initValue="receipt.paperTitleType"
			:showItemNum="2" 
			:isCanInput="false"  
			:placeholder = "'请选择抬头类型'"
			@change="ischange1"
			>
			<!-- :style_Container="listBoxStyle" -->
			</xfl-select>
			
		</view>
		<view class="row default-row">
			<text class="tit">发票抬头</text>
			<input class="input" type="text" v-model="receipt.paperTitle" placeholder="必填" placeholder-class="placeholder"/>
		</view>
		<view class="row default-row" v-if="receipt.paperTitleType==='企业'"> 
			<text class="tit">纳税人编号</text>
			<input class="input" type="text" v-model="receipt.TaxPayerId" placeholder="4423422122Y" placeholder-class="placeholder" />
		</view>
		
		<view class="row default-row" v-if="receipt.paperTitleType==='企业'">
			<text class="tit">开户银行</text>
			<input class="input" type="text" v-model="receipt.bank" placeholder="选填" placeholder-class="placeholder" />
		</view>
		<view class="row default-row" v-if="receipt.paperTitleType==='企业'">
			<text class="tit">银行账号</text>
			<input class="input" type="text" v-model="receipt.bankId" placeholder="选填" placeholder-class="placeholder" />
		</view>
		<view class="row default-row" v-if="receipt.paperTitleType==='企业'">
			<text class="tit">企业地址</text>
			<input class="input" type="text" v-model="receipt.companyAddress" placeholder="选填" placeholder-class="placeholder" />
		</view>
		<view class="row default-row" v-if="receipt.paperTitleType==='企业'">
			<text class="tit">企业电话</text>
			<input class="input" type="number" v-model="receipt.companyMobile" placeholder="选填" placeholder-class="placeholder" />
		</view>
		<view class="row default-row">
			<text class="tit">接收邮箱</text>
			<input class="input" type="text" v-model="receipt.reciveEmail" placeholder="必填" placeholder-class="placeholder" />
		</view>
		<view class="row default-row">
			<text class="tit">设为默认发票信息</text>
			<switch :checked="receipt.defaultt" color="#fa436a" @change="switchChange" />
		</view>
		<view class="uni-btn-v">
			<button type="default" @click="back">本次不开具发票，继续下单&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;></button>
		</view>
		<button class="add-btn" @click="confirm">完成</button>
	</view>
</template>

<script>
	import xflSelect from 'components/xfl-select/xfl-select.vue';
	import {
		mapMutations,
		mapState
	} from 'vuex';
	
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
				receipt: {
					paperType: '',
					paperTitleType: '',
					paperTitle: '',
					TaxPayerId:[],
					bank:"",
					bankId: '',
					companyAddress: '',
					companyMobile:'',
					reciveEmail:'',
					defaultt: false,
				}
			}
		},
		onLoad(){
			this.receipt.paperType = '电子发票';
			this.receipt.paperTitleType='个人或事业单位'
		},
		/* back() {
			uni.navigateBack({
				delta: 1
			})
		}, */
		computed: {
			...mapState(['userInfo'])
		},
		methods: {
			ischange(data){
				//console.log("ffff"+JSON.stringify(data));
				//this.receipt.paperType = this.list[data.index];
				this.receipt.paperType = data.newVal;
				console.log(this.receipt.paperType);
			},
			ischange1(data){
				//console.log("ffff"+JSON.stringify(data));
				//this.receipt.paperType = this.list1[data.index];
				this.receipt.paperTitleType = data.newVal;
				console.log(this.receipt.paperTitleType);
			},
			back(){
				uni.navigateBack({
					delta: 1
				})
			},
			switchChange(e){
				this.receipt.defaultt = e.target.value;
				console.log(e.target.value)
			},
			/* visibleChange(isShow){ 
				// 列表框的显示隐藏事件
				console.log('isShow::', isShow);
			}, */
			//地图选择地址
			/* chooseLocation(){
				uni.chooseLocation({
					success: (data)=> {
						this.addressData.addressName = data.name;
						this.addressData.address = data.name;
					}
				})
			}, */
			
			//提交
			async confirm(){
				let data = this.receipt;
				console.log(data);
				if(!data.paperTitle){
					this.$api.msg('请填写发票抬头');
					return;
				}
				if(!data.TaxPayerId){
					this.$api.msg('请填写纳税人编号');
					return;
				}
				if(!data.reciveEmail){
					this.$api.msg('请填写接收邮箱');
					return;
				}
				const {
					paperType,
					paperTitleType,
					paperTitle,
					TaxPayerId,
					bank,
					bankId,
					companyAddress,
					companyMobile,
					reciveEmail,
					defaultt,
				} = this;
				const [err, res] = await uni.request({
					url: "http://10.141.53.7:8080/MemberCenter/api/v1/members/",
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded',
						'dvcId': '',
						'chnlTpCd': '2202',
						'stmNo': '0657'
					},
					data: {
						userID:this.userInfo.a,
						usrInvTpCd:paperType,
						usrInvLkupTpCd:paperTitleType,
						usrInvLkupNm:paperTitle,
						usrInvTaxPayerNo:TaxPayerId,
						usrInvOpnAccBnkNm:bank,
						usrInvOpnAccBnkNo:bankId,
						usrInvEntpAddr:companyAddress,
						usrInvEntpTel:companyMobile,
						reciveEmail:reciveEmail,
						isDefault: defaultt,
					}
				});
				if (err) {
					console.log('request fail', err);
					uni.showModal({
						content: err.errMsg,
						showCancel: false,
					});
				} else {
					console.log('request success', res)
					uni.showToast({
						title: '请求成功',
						icon: 'success',
						mask: true,
						duration: 2000
					});
					console.log(JSON.stringify(res))
				}
				//this.$api.prePage()获取上一页实例，可直接调用上页所有数据和方法，在App.vue定义
				/* this.$api.prePage().refreshList(data, this.manageType);
				this.$api.msg(`地址${this.manageType=='edit' ? '修改': '添加'}成功`); */
				if (res.data.returnCode == 200000) {
					console.log("res.data:"+res.data.returnMessage);
					uni.navigateBack();
				} else {
					this.$api.msg(res.data.returnMessage);
				}
				
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
