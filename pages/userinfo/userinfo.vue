<template>
	<view>
		<view class="container">
			<view><text class="message-1">常规设置</text></view>
			<view class="nickname-box">
				<text class="nickname">当前昵称:{{nickname}}</text><br />
				<text class="nickname">更改昵称:</text>
				<input class="nickname-input" type="text" placeholder="输入新的昵称" v-model="renickname" required="required" />
			</view>
			<view class="avatar-box">
				<text class="">更换头像</text>
				<view class="kong"></view>
				<image :src="avatar" class="avatar" @tap="showActionSheet"></image>
			</view>
			<view>
				<uni-list-item title="更改性别" note="男"></uni-list-item>
				<uni-list-item title="更改生日" note="1999-02-15"></uni-list-item>
				<uni-list-item title="更改主页"></uni-list-item>
				<uni-list-item title="更改个人简介" note="我本将心向明月,奈何明月照沟渠"></uni-list-item>
			</view>
			<view class="password-box">
				<view><text class="message-1">安全设置</text></view>
				<navigator url="../userinfo/newpsd">
					<uni-list-item title="重置密码"></uni-list-item>
				</navigator>
				<uni-list-item title="绑定微信" thumb="https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=3336519328,2903738409&fm=26&gp=0.jpg"></uni-list-item>
				<uni-list-item title="绑定微博" thumb="http://wuyou-soft.oss-cn-hangzhou.aliyuncs.com/%E5%BE%AE%E5%8D%9A...png"></uni-list-item>
			</view>

				<button class="update" @tap="updateUser(renickname)">确认修改</button>
		</view>
	</view>
</template>

<script>
	import uniList from '@dcloudio/uni-ui/lib/uni-list/uni-list.vue';
	import uniListItem from '@dcloudio/uni-ui/lib/uni-list-item/uni-list-item.vue';
	export default {
		components: {
			uniList,
			uniListItem
		},
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
												url: "http://112.74.177.53:8080/api/user/avatar",
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
														url: 'http://112.74.177.53:8080/api/user/' +
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
										url: "http://112.74.177.53:8080/api//user/avatar",
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
												url: 'http://112.74.177.53:8080/api/user/' +
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

			updateUser: function(renickname) {
				var _this = this;
				uni.request({
					url: this.apiServer + '/user/nickname?id=' + uni.getStorageSync('login_key').userId,
					method: 'put',
					data: renickname,
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
								}
							},
						});
						uni.showToast({
							title: '修改成功！',
							duration: 2500
						});
					}
				});

			}


		}

	};
</script>

<style scoped>
	.message-1 {
		font-size: 18px;
		color: #DE533A;
	}

	.container {
		display: flex;
		flex-direction: column;
		font-size: 18px;
	}

	.nickname-box {
		width: 100%;
		height: 90px;
		background-color: #fff;
		border-bottom: 1px solid rgb(227, 227, 230);
		margin-bottom: 13px;
		padding-left: 14px;
	}

	.avatar-box {
		display: flex;
		width: 100%;
		height: 70px;
		background-color: #fff;
		border-bottom: 1px solid rgb(227, 227, 230);
		padding-left: 14px;
	}

	.password-box {
		width: 100%;
		/* height: 123px; */
		background-color: #fff;
		margin-bottom: 10px;
	}

	.kong {
		width: 40px;
	}
	.update{
		width: 50%;
		height: 58px;
		border-radius: 10px;
		background-color: rgb(234, 111, 90);
		color: rgb(255,255,255);
		border: none;
		outline: none;
		display: flex;
		justify-content: center;
		align-items: center;
	}
</style>
