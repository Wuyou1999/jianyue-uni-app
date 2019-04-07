<template>
	<view class="container">
		<view class="list">
			<view><text class="message-1">消息推送</text></view>
			<uni-list>
				<uni-list-item title="推送功能" show-switch="true" show-arrow="false" @switchChange="black"></uni-list-item>
				<uni-list-item title="文章推送" show-badge="true" badge-text="5" v-show="followed === true"></uni-list-item>
				<uni-list-item title="新消息推送" show-badge="true" badge-text="99+" v-show="followed === true"></uni-list-item>
			</uni-list>
			<view><text class="message-1">通用设置</text></view>
			<uni-list>
				<navigator url="../userinfo/userinfo" >
					<uni-list-item title="编辑个人资料" ></uni-list-item>
				</navigator>
				<uni-list-item title="默认编辑器" ></uni-list-item>
				<uni-list-item title="添加文章到封面" ></uni-list-item>
			</uni-list>
			<view><text class="message-1">其他</text></view>
			<uni-list>
				<uni-list-item title="回收站" ></uni-list-item>
			</uni-list>
			<view class="list-item">
				<button type="primary" @tap="logout" class="out">退出当前账号</button>
			</view>
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
				followed:false
			};
		},
		onLoad() {
			uni.setNavigationBarTitle({
				title: '设置'
			});
		},
		methods: {
			logout: function() {
				console.log('log out');
				uni.removeStorageSync('login_key');
				uni.showToast({
					title: '已经退出当前账号'
				});
				uni.navigateBack();
			},
			black:function(){
				this.followed=!this.followed;
			}
		}
	};
</script>

<style scoped>
	.out{
		margin-top: 20px;
	}
	.message-1{
		font-size: 15px;
		color: #DE533A;
	}
</style>
