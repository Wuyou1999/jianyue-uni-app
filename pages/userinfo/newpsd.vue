<template>
	<view class="container">
		<input class="uni-input" password type="text" placeholder="请输入新密码" v-model="repassword" required="required" />
		<input class="uni-input" password type="text" placeholder="确认密码" v-model="repassword" required="required" />
		<button type="primary" @tap="updateUser(repassword)">确认</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				nickname: uni.getStorageSync('login_key').nickname,
				avatar: uni.getStorageSync('login_key').avatar,
				userId: uni.getStorageSync('login_key').userId
			};
		},
		onLoad() {
			uni.setNavigationBarTitle({
				title: '密码修改'
			});
		},
		methods: {
			updateUser: function(repassword) {
				uni.showLoading({
					title: '正在修改'
				})
				var _this = this;
				uni.request({
					url: this.apiServer + '/user/password?id=' + uni.getStorageSync('login_key').userId,
					method: 'put',
					data: repassword,
					header: {
						'content-type': 'application/json'
					},
					success: res => {
						console.log('save');
						uni.request({
							url: this.apiServer + '/user/' +
								uni.getStorageSync('login_key').userId,
							method: 'GET',
							data: {
								userId: _this.userId
							},
							header: {
								'content-type': 'application/json'
							},
							success: res => {
								console.log('save2');
								if (res.data.code == 0) {
									//将用户数据记录在本地存储
									uni.setStorageSync('login_key', {
										userId: res.data.data.id,
										nickname: res.data.data.nickname,
										avatar: res.data.data.avatar,
										token: res.data.data.token,
										credit: res.data.data.credit,
										login: true
									});
									uni.hideLoading()
									uni.showToast({
										title: '修改成功！',
										duration: 2500
									});
									console.log('log out');
									uni.removeStorageSync('login_key');
									uni.showToast({
										title: '请重新登录',
										duration: 2500
									});
									uni.navigateTo({
										url: '../signin/signin'
									});
								}
							},
						});

					}
				})


			}
		}


	}
</script>

<style scoped>
	.card {
		display: flex;
		border: none;
		margin-left: 40px;
		margin-top: 80px;
		margin-bottom: 23px;
		align-items: center;
	}

	input {
		height: 50px;
		border-bottom: 1px solid #eee;
		margin-bottom: 5px;
	}
</style>
