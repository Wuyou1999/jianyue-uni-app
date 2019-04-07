<template>
	<view>
		<view class="container">
			<view class="nickname-box">
				<text class="nickname"> 当前昵称:{{nickname}}</text><br />
				<text class="nickname">修改昵称:</text>
				<input class="nickname-input" type="text" placeholder="输入新的昵称" v-model="renickname" required="required" />
			</view>

			<view class="avatar-box">
				<text class="">更换头像</text>
				<view class="kong"></view>
				<image :src="avatar" class="avatar" @tap="showActionSheet"></image>
			</view>
			<view class="password-box">
				<text>修改密码:</text>
				<input class="uni-input" password type="text" placeholder="输入新的密码" v-model="repassword" required="required" />
			</view>
			<view class="list-item">
				<button type="primary" @tap="updateUser(renickname,repassword)">确认修改</button>
			</view>

		</view>
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
				title: '个人资料修改'
			});
		},
		methods: {
			showActionSheet: function() {
				var _this = this;
				uni.showActionSheet({
					itemList: ['拍照', '从相册选择'],
					success: function(res) {
						console.log('选中了第' + (res.tapIndex + 1) + '个按钮');
						//选择的是拍照功能
						if (res.tapIndex == 0) {
							uni.chooseImage({
								count: 1,
								sourceType: ['camera'],
								success: function(res) {
									uni.saveImageToPhotosAlbum({
										filePath: res.tempFilePaths[0],
										success: function() {
											console.log('save success');
											uni.uploadFile({
												url: "http://localhost:8080/api/user/avatar",
												filePath: res.tempFilePaths[0],
												name: 'file',
												formData: {
													userId: _this.userId
												},
												success: uploadFileRes => {
													console.log(uploadFileRes.data);
													_this.avatar = uploadFileRes.data;
												},
												complete: function() {
													console.log('save');
													uni.request({
														url: 'http://localhost:8080/api/user/' +
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
																	login: true
																});
															}
														},
													});
												}

											});
										}
									});
								}
							});
						}
						//从相册选择
						if (res.tapIndex == 1) {
							uni.chooseImage({
								count: 1, //默认9
								sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都
								sourceType: ['album'], //从相册选择
								success: function(res) {
									console.log(JSON.stringify(res.tempFilePaths));
									uni.uploadFile({
										url: "http://localhost:8080/api/user/avatar",
										filePath: res.tempFilePaths[0],
										name: 'file',
										formData: {
											userId: _this.userId
										},
										success: uploadFileRes => {
											console.log(uploadFileRes.data);
											_this.avatar = uploadFileRes.data;
										},
										complete: function() {
											console.log('save');
											uni.request({
												url: 'http://localhost:8080/api/user/' +
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
															login: true
														});
													}
												},
											});
										},

									});
								}
							});
						}
					},
					fail: function(res) {
						console.log(res.errMsg);
					}
				});
			},

			updateUser: function(renickname,repassword) {
				var _this = this;
				uni.request({
					url: this.apiServer +'/user/nickname?id=' + uni.getStorageSync('login_key').userId,
					method: 'put',
					data: renickname,
					header: {
						'content-type': 'application/json'
					},
					success: res => {
						console.log('save');
						uni.request({
							url: this.apiServer +'/user/' +
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
										login: true
									});
								}
							},
						});

					}
				});
				uni.request({
					url: this.apiServer +'/user/password?id=' + uni.getStorageSync('login_key').userId,
					method: 'put',
					data: repassword,
					header: {
						'content-type': 'application/json'
					},
					success: res => {
						console.log('save');
						uni.request({
							url: this.apiServer +'/user/' +
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
										login: true
									});
								}
							},
						});

						uni.showToast({
							title: '修改成功！'
						});
						/* uni.navigateBack(); */

					}
				});
			}
			

		}

	};
</script>

<style scoped>
	.container {
		display: flex;
		flex-direction: column;
	}

	.nickname-box {
		width: 100%;
		height: 100px;
		background-color: #fff;
		border-bottom: 1px solid #eee;

	}

	.avatar-box {
		display: flex;
		width: 100%;
		height: 70px;
		background-color: #fff;
		border-bottom: 1px solid #eee;

	}

	.password-box {
		width: 100%;
		height: 70px;
		background-color: #fff;
	}

	.kong {
		width: 40px;
	}
</style>
