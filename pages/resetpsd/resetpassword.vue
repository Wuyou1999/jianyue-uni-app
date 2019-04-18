<template>
	<view class="container">
		<input class="uni-input" password type="text" placeholder="输入新密码" v-model="newpassword" required="required" />
		<input class="uni-input" password type="text" placeholder="确认新密码" required="required" />
		<button class="loginbutt" @tap="resetpsd(newpassword)">重置密码</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				mobile: '',
			};
		},
		onLoad: function(option) {
			//option为object类型，会序列化上个页面传递的参数
			console.log(option.mobile);
			this.mobile = option.mobile;
		},
		methods: {
			resetpsd: function(newpassword) {
				var _this = this;
				uni.request({
					url: this.apiServer + '/user/newpassword?mobile=' + _this.mobile,
					method: 'PUT',
					data: newpassword,
					header: {
						'content-type': 'application/json'
					},
					success: res => {
						uni.showModal({
								title: '重置密码成功',
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
						
					},
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
