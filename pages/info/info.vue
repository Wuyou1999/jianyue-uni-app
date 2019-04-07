<template>
	<view>
		<view class="uni-flex uni-column container">
			<input class="uni-input" password type="text" placeholder="请输入昵称" v-model="user.password" required="required" />
			<input class="uni-input" type="number" placeholder="请输入手机号" v-model="user.mobile" required="required" />
			<input class="uni-input" password type="text" placeholder="请输入密码" v-model="user.password" required="required" />
			
			<button type="primary" @click="update(user)">确认修改</button>
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				id: this.$route.params.id,
				user: {
					/* mobile: '',
					password: '',
					avatar:'' */
				}
			};
		},
		methods: {
			update: function(user) {
				var _this = this;
				uni.request('http://112.74.177.53:8080/api/user/user/' + this.id).then(function(response) {
					_this.user = response.data;
				});
				success: res => ({
					method: 'put',
					url: 'http://112.74.177.53:8080/api/user/info',
					data: {
						id: user.id,
						mobile: user.mobile,
						password: user.password,
						nickname: user.nickname,
						avatar: user.avatar,
						regtime: user.regtime,
						status: user.status,
						token: user.token
					}
				});
				uni.showToast({
					title: '修改成功',

				});
			},
			created() {
				var _this = this;
				this.$http.get('http://112.74.177.53:8080/api/user/user/' + this.id).then(function(response) {
					_this.course = response.data;
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
