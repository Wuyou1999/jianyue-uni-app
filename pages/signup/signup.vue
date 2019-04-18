<template>
	<view class=" container">
		<view class="welcome">
			<image src="../../static/lihua.png"></image>
			<text>欢迎使用简阅</text>
		</view>
		<text class="mobilenum">请输入您的手机号:</text>

		<view class="input1">
			<select class="select">
				<option value="+86">+86</option>
				<option value="+95">+95</option>
			</select>
			<input class="uni-input" type="number" placeholder="输入手机号" v-model="mobile" required="required" />
		</view>

		<view class="code">
			<text class="code1">验证码为：{{verifyCode}}</text>
			<button class="message" @tap="getVerifyCode" :disabled="show">{{title}}</button>
		</view>

		<checkbox-group>
			<label>
				<checkbox value="同意"  color="#2dc3cc" />同意《简阅用户注册协议》</label>
		</checkbox-group>
		<view class="content">
			<lausirCodeDialog :show="showCodeDialog" :len="6" :autoCountdown="true" :phone="mobile" v-on:change="change" v-model="verifyCode"></lausirCodeDialog>
		</view>
		<button class="loginbutt" @tap="checkCode" :disabled="followed">下一步</button>
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
				timer: 60,
				show: false,
				followed: true,
				title: '获取验证码',
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
				let timer1 = setInterval(() => {
					_this.show = true;
					_this.followed = false;
					_this.timer--;
					_this.title = _this.timer + 's后再试';
					if (_this.timer == 0) {
						clearInterval(timer1);
						_this.timer = 60;
						_this.show = false;
						_this.title = '发送验证码';
						return;
					}
				}, 1000);
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
							this.showCodeDialog = true;
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
	.welcome {
		display: flex;
		justify-content: center;
		margin: 0 auto;
		margin-bottom: 30px;
		text-align: center;
		color: rgb(168, 168, 168);
		font-size: 23px;
		margin-top: 20px;
	}

	.welcome image {
		width: 55px;
		height: 35px;

	}

	.mobilenum {
		font-weight: bold;
		font-size: 20px;
	}

	input {
		height: 50px;
		margin: 0 auto;
		width: 75%;
		border-bottom: 1px solid #eee;
		margin-bottom: 5px;

	}

	.input1 {
		display: flex;
	}

	.select {
		width: 55px;
		height: 50px;
		font-weight: bold;
	}

	.mobile-input {
		display: flex;
		height: 50px;
		border-bottom: 1px solid #eee;
	}

	.message {
		background-color: rgb(26, 173, 25);
		height: 47px;
		width: 30%;
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

	.code {
		display: flex;
		padding-top: 20upx;
		margin-bottom: 20px;
	}

	.code1 {
		font-size: 20px;
	}

	.message {
		position: absolute;
		right: 10px;
		top: 150px;
	}
	
	.loginbutt {
		margin: 0 auto;
		width: 80%;
		border-radius: 30px;
		background-color: #EA6F5A;
		color: white;
	}
	
	.loginbutt:hover {
		background-color:rgb(229, 140, 124);
	}
</style>
