<template>
	<view class="container">
		<input class="uni-input" password type="text" placeholder="输入新密码" v-model="newpassword" required="required" />
		<button type="primary" @tap="resetpsd(newpassword)">重置密码</button>
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
						uni.showToast({
							title: '修改成功！'
						});
						uni.navigateTo({
							url: '../signin/signin'
						});
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
</style>
