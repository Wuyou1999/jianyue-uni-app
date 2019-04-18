<template>
	<view class="container">
		<input class="uni-input" password type="text" placeholder="输入密码" v-model="password" required="required" />
		<input class="uni-input" password type="text" placeholder="确认密码" required="required" />
		<button class="loginbutt" @tap="signUp(userDTO)">注册</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userDTO: {
					mobile: '',
					password: ''
				}
			};
		},
		onLoad: function(option) {
			title: '设置密码'
			//option为object类型，会序列化上个页面传递的参数
			console.log(option.mobile);
			this.mobile = option.mobile;
		},
		methods: {
			signUp: function(userDTO) {
				var _this = this;
				uni.request({
					url: this.apiServer + '/user/sign_up',
					method: 'POST',
					header: {
						'content-type': 'application/json'
					},
					data: {
						mobile: _this.mobile,
						password: _this.password
					},
					success: res => {
						if (res.data.code === 0) {
							uni.showModal({
									title: '注册成功',
									content: '现在去登录吗？',
									cancelText: '再等等',
									cancelColor: '#EA6F5A',
									success: function(res) {
										if (res.confirm) {
											uni.navigateTo({
												url: '../signin/signin'
											});
										} else if (res.cancel) {
											uni.navigateTo({
												url: '../index/index'
											});
										};
									}
							
								})
						} else {
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
	input {
		height: 50px;
		border-bottom: 1px solid #eee;
		margin-bottom: 5px;
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
