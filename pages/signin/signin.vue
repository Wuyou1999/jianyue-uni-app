<template>
	<view class="container">
		<view class="avatar-box">
			<image src="../../static/default.png" mode="scaleToFill" class="avatar"></image>
		</view>
		<input class="input" type="number" placeholder="请输入手机号" v-model="userDTO.mobile" required="required" />
		<input class="input" password type="text" placeholder="请输入密码" v-model="userDTO.password" required="required" />
		<button class="loginbutt" @tap="signIn(userDTO)">登录</button>
		<view class="rgister">
			<navigator url="../signup/signup" class="tip">还没账号？点击注册</navigator>
			<view class="kong"></view>
			<navigator url="../resetpsd/resetpsd" class="tip2">忘记密码？点击重置</navigator>
		</view>
		<view class="disanf1">
			<text>—— 社交账号直接登录 ——</text>
		</view>
		<view class="disanf">
			<image src="../../static/weixin.png"></image>
			<text class="sj">微信</text>
			<view class="kong1"></view>
			<image src="../../static/qq.png"></image>
			<text class="sj">QQ</text>
			<view class="kong1"></view>
			<image src="../../static/weibo.png"></image>
			<text class="sj">微博</text>
		</view>
	</view>
</template>

<script>
	import xyDialog from '@/components/xy-dialog/xy-dialog.vue';
	export default {
		components: {
			xyDialog
		},
		data() {
			return {
				userDTO: {
					mobile: '',
					password: ''
				}
			};
		},
		onLoad() {
			uni.setNavigationBarTitle({
				title: '登录'
			});
		},
		methods: {
			signIn: function(userDTO) {
				var _this = this;
				// console.log(userDTO.mobile + ',' + userDTO.password);
				uni.showLoading({
					title: '正在登录'
				})
				uni.request({
					url: this.apiServer + '/user/sign_in',
					method: 'POST',
					data: {
						mobile: userDTO.mobile,
						password: userDTO.password
					},
					header: {
						'content-type': 'application/json'
					},
					success: res => {

						// console.log(res.data.data);
						if (res.data.code == 0) {
							//将用户数据记录在本地存储
							uni.setStorageSync('login_key', {
								userId: res.data.data.id,
								nickname: res.data.data.nickname,
								avatar: res.data.data.avatar,
								token: res.data.data.token,
								credit: res.data.data.credit,
								login: true
							})
							uni.hideLoading()
							uni.showToast({
								title: '登录成功！',
								duration: 2500
							})
							uni.switchTab({
								url: '../my/my'
							});

						}
						//登录失败，弹出各种原因
						else {
							uni.hideLoading()
							uni.showModal({
								title: '提示',
								content: res.data.msg
							});
						}
					}
				});
			}
		}
	};
</script>

<style scoped>
	.avatar-box {
		margin-top: 25px;
		display: flex;
		/* align-items: center; */
		justify-content: center;
		margin-bottom: 15px;
	}

	.input {
		margin: 0 auto;
		height: 50px;
		width: 90%;
		border-bottom: 1px solid #eee;
		margin-bottom: 15px;
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

	.disanf1 {
		padding-top: 225px;
		display: flex;
		text-align: center;
		justify-content: center;
		font-size: 18px;
		color: #6f6f6f;
	}

	.disanf {
		display: flex;
		justify-content: center;

	}

	.disanf image {
		width: 40px;
		height: 40px;
		justify-content: center;
	}

	.rgister {
		display: flex;
		padding-top: 25px;
		margin: 0 auto;
		width: 90%;
	}

	.kong {
		width: 105upx;
	}

	.kong1 {
		width: 90upx;
	}

	.tip {
		color: rgb(26, 160, 52);
		font-size: 16px;
	}

	.tip2 {
		color: rgb(224, 96, 73);
		font-size: 16px;
	}
	
	.sj{
		padding-top: 10px;
		font-weight: bold;
	}
</style>
