<template>
	<view class=" container">

		<text class="mobilenum">请输入手机号:</text>
		<input class="uni-input" type="number" placeholder="输入手机号" v-model="mobile" required="required" />
		<!-- <view class="mobile-input">
			<text>请输入手机验证码:</text>
			<button class="message" @tap="getVerifyCode">发送验证短信</button>
		</view>
		<input class="uni-input" type="text" placeholder="输入收到的手机验证码" v-model="verifyCode" required="required" /> -->
		<button class="message" @tap="getVerifyCode">发送验证码</button>
		<view class="code">
			<text>验证码为：{{verifyCode}}</text>
		</view >
		<view class="content">
			
			<lausirCodeDialog :show="showCodeDialog" :len="6" :autoCountdown="true" :phone="mobile" v-on:change="change" v-model="verifyCode"></lausirCodeDialog>
		</view>
		
		
		<button type="primary" @tap="checkCode">下一步</button>
	</view>
</template>

<script>
	import lausirCodeDialog from '../../components/lausir-codedialog/lausir-codedialog.vue';

	export default {
		components: {
			lausirCodeDialog,
		},
		data() {
			return {
				showCodeDialog: false,
				mobile: '',
				verifyCode: ''
			};
		},
		onLoad() {
			uni.setNavigationBarTitle({
				title: '注册'
			});
		},
		methods: {
			getVerifyCode: function() {
				this.showCodeDialog = true;
				var _this = this;
				uni.request({
					url: this.apiServer + '/user/verify',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						mobile: _this.mobile
					},
					success: res => {
						if (res.data.code === 0) {
							uni.showToast({
								title: '验证码已发送'
							});
							_this.disabled = true;
							console.log(_this.disabled);
						} else {
							uni.showModal({
								title: '提示',
								content: res.data.msg
							});
						}
					}
				});
			},
			checkCode: function() {
				var _this = this;
				console.log(_this.verifyCode);
				uni.request({
					url: this.apiServer + '/user/check',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						mobile: _this.mobile,
						verifyCode: _this.verifyCode
					},
					success: res => {
						console.log(res.data);
						if (res.data.code === 0) {
							uni.navigateTo({
								url: '../password/password?mobile=' + _this.mobile
							});
						} else {
							uni.showModal({
								title: '提示',
								content: res.data.msg
							});
						}
					}
				});
			},

			change: function(res) {
				if (res.type == 1) {
					this.verifyCode = res.code;
					this.showCodeDialog = false;
				} else if (res.type == -1) {
					this.verifyCode = "请输入验证码";
					this.showCodeDialog = false;
				} else {
					setTimeout(function() {
						res.resendCall()
					}, 3000)
				}


			}
		}

	};
</script>

<style scoped>
	input {
		height: 50px;
		border-bottom: 1px solid #eee;
		margin-bottom: 5px;
	}

	.mobile-input {
		display: flex;
		height: 50px;
		border-bottom: 1px solid #eee;
	}

	.message {
		background-color: rgb(26, 160, 52);
		height: 47px;
		width: 45%;
		color: #FFFFFF;
		outline: none;
		float: left;
	}


	.content {
		text-align: center;
		height: 50upx;
	}

	.logo {
		height: 200upx;
		width: 200upx;
		margin-top: 200upx;
	}

	.title {
		font-size: 36upx;
		color: #8f8f94;
	}
	.code{
		
		padding-top: 20upx;
	}
</style>
