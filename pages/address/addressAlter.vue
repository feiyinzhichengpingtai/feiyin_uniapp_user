<template>
	<view class="content">
		<view class="row b-b">
			<text class="tit">收货人:</text>
			<input class="input" type="text" v-model="addressData.name" placeholder="收货人姓名" placeholder-class="placeholder" />
		</view>
		<view class="row b-b">
			<text class="tit">手机号码：</text>
			<input class="input" type="number" v-model="addressData.mobile" placeholder="收货人手机号码" placeholder-class="placeholder" />
		</view>
		<view class="row b-b">
			<text class="tit">所在地区：</text>
			<text @click="chooseLocation" class="input">
				{{addressData.label}}
			</text>
			<!-- <text class="yticon icon-shouhuodizhi"></text> -->
		</view>
		<view class="row b-b"> 
			<text class="tit">详细地址：</text>
			<input class="input" type="text" v-model="addressData.area" placeholder="请输入详细地址" placeholder-class="placeholder" />
		</view>
		
		<view class="row default-row">
			<text class="tit">设为默认地址：</text>
			<switch :checked="addressData.defaule" color="#fa436a" @change="switchChange" />
		</view>
		<view class="row default-row">
			<text class="tit">标签：</text>
			
		</view>
		<view class="row b-b">
			<view v-for="(item, index) in tags" :class="{ checked: item.checked }" :key="index" class="calendar-tags" @click="toggle(index, item)">
				<view class="calendar-tags-item">{{ item.name }}</view>
			</view>
		</view>
		<button class="del-btn" v-if="edit" @click="del">删除</button>
		<button class="add-btn" @click="confirm">提交</button>
		<mpvue-city-picker :themeColor="themeColor" ref="mpvueCityPicker" :pickerValueDefault="cityPickerValue" @onCancel="onCancel" @onConfirm="onConfirm"></mpvue-city-picker>
	</view>
</template>

<script>
	import mpvueCityPicker from '@/components/mpvue-citypicker/mpvueCityPicker.vue'
		
	export default {
		components: {
			mpvueCityPicker
		},
		data() {
			let tags = [{
					id: 0,
					name: '家',
					checked: false,
					attr: 'home'
				},
				{
					id: 1,
					name: '公司',
					checked: false,
					attr: 'company'
				},
				{
					id: 2,
					name: '学校',
					checked: false,
					attr: 'endDate'
				},
			]
			return {
				tags,
				edit:false,
				themeColor: '#007AFF',
				cityPickerValue: [0, 0, 1],
				addressData: {
					name: '',
					mobile: '',
					label: '请点击选择地址',
					address: '',
					area: '',
					default: false
				}
			}
		},
		onLoad(option){
			if(option.type==='edit'){
				this.edit=true;
				this.addressData = JSON.parse(option.data)
			}
			this.manageType = option.type;
		},
		methods: {
			switchChange(e){
				this.addressData.default = e.detail;
			},
			
			//地图选择地址
			chooseLocation(){
				this.$refs.mpvueCityPicker.show()
				// uni.chooseLocation({
				// 	success: (data)=> {
				// 		this.addressData.addressName = data.name;
				// 		this.addressData.address = data.name;
				// 	}
				// })
			},
			onCancel(e) {
				console.log(e)
			},
			onConfirm(e) {
				this.addressData = e;
				this.cityPickerValue = e.value;
			},
			//提交
			confirm(){
				let data = this.addressData;
				if(!data.name){
					this.$api.msg('请填写收货人姓名');
					return;
				}
				if(!/(^1[3|4|5|7|8][0-9]{9}$)/.test(data.mobile)){
					this.$api.msg('请输入正确的手机号码');
					return;
				}
				if(!data.address){
					// this.$api.msg('请在地图选择所在位置');
					return;
				}
				if(!data.area){
					this.$api.msg('请填写门牌号信息');
					return;
				}
				
				//this.$api.prePage()获取上一页实例，可直接调用上页所有数据和方法，在App.vue定义
				this.$api.prePage().refreshList(data, this.manageType);
				this.$api.msg(`地址${this.manageType=='edit' ? '修改': '添加'}成功`);
				setTimeout(()=>{
					uni.navigateBack()
				}, 800)
			},
			//删除
			del(){
				
			},
		}
	}
</script>

<style lang="scss">
	page{
		background: $page-color-base;
		padding-top: 16upx;
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
			width: 150upx;
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
		.calendar-tags-item {
			width: 150upx;
			font-size: 30upx;
			padding: 5upx 5upx;
			border: 1px rgba(0, 0, 0, 0.2) solid;
			color: #333;
			border-radius: 10upx;
			text-align: center;
			margin: 10upx;
			background: #f8f8f8;
		}
		.calendar-tags {
			width: 100%;
			box-sizing: border-box;
		}
		.checked .calendar-tags-item {
			background: rgb(0, 122, 255);
			color: #fff;
			border: 1px rgba(0, 0, 0, 0.1) solid;
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
	.del-btn{
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
