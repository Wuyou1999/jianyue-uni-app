<template>
	<view class="container">
		<view class="list">
			<view>
				<text class="message-1">消息推送</text>
			</view>
			<uni-list-item title="推送功能" show-switch="true" show-arrow="false" switch-checked="true" @switchChange="black"></uni-list-item>
			<uni-list-item title="文章推送" show-badge="true" badge-text="5" v-show="followed === false"></uni-list-item>
			<uni-list-item title="新消息推送" show-badge="true" badge-text="99+" v-show="followed === false"></uni-list-item>
			<view>
				<text class="message-1">通用设置</text>
			</view>
			<navigator url="../userinfo/userinfo">
				<uni-list-item title="编辑个人资料"></uni-list-item>
			</navigator>
			<uni-list-item title="默认编辑器"></uni-list-item>
			<uni-list-item title="添加文章到封面"></uni-list-item>
			<uni-list-item title="赞赏设置"></uni-list-item>
			<uni-list-item title="字号设置"></uni-list-item>
			<uni-list-item title="隐私设置"></uni-list-item>
			<view><text class="message-1">其他</text></view>
			<uni-list-item title="回收站"></uni-list-item>
			<button class="out" @tap="logout">退出当前账号</button>

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
				followed: false
			};
		},
		onLoad() {
			uni.setNavigationBarTitle({
				title: '设置'
			});
		},
		methods: {
			logout: function() {
				uni.showLoading({
					title: '正在退出'
				})
				console.log('log out');
				uni.hideLoading()
				uni.showModal({
					title: '提示',
					content: '确认退出吗？',
					cancelText: '再等等',
					cancelColor: '#EA6F5A',
					success: function(res) {
						if (res.confirm) {
							uni.navigateBack();
							uni.removeStorageSync('login_key');
						} else if (res.cancel) {
							console.log('用户点击取消')
						};
					}
				});

			},
			black: function() {
				this.followed = !this.followed;
			}
		}
	};
</script>

<style scoped>
	.out {
		margin-top: 20px;
		width: 50%;
		display: flex;
		justify-content: center;
		border: 1px solid rgb(222, 84, 60);
		color: rgb(222, 84, 60);
		background-color: rgb(255, 255, 255);
	}

	.message-1 {
		font-size: 18px;
		color: #DE533A;
	}
</style>
