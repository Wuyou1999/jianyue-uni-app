<template>
	<view class="uni-flex uni-column container">
		<view class="avatar-box">
			<image src="../../static/default.png" mode="scaleToFill" class="avatar"></image>
		</view>
		<input class="uni-input" type="number" placeholder="输入手机号" v-model="userDTO.mobile" required="required" />
		<input class="uni-input" password type="text" placeholder="输入密码" v-model="userDTO.password" required="required" />
		<button type="primary" @tap="signIn(userDTO)">登录</button>
		<view class="rgister">
			<navigator url="../signup/signup">还没账号？点击注册</navigator>
			<view class="kong"></view>
			<navigator url="../resetpsd/resetpsd">忘记密码？点击重置</navigator>
		</view>
	</view>
</template>

<script>
	import xyDialog from '@/components/xy-dialog/xy-dialog.vue';
	export default {
		components: {xyDialog},
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
								login: true
							})
							uni.showToast({
								title: '登录成功！'
							});
							uni.switchTab({
								url: '../my/my'
							});

						}
						//登录失败，弹出各种原因
						else {
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
		display: flex;
		/* align-items: center; */
		justify-content: center;
		margin-bottom: 15px;
	}

	input {
		height: 50px;
		border-bottom: 1px solid #eee;
		margin-bottom: 5px;
	}

	.rgister {
		display: flex;
		justify-content: center;
		margin-top: 20px;
	}
	.kong {
		width: 25upx;
	}
</style>
