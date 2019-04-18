<template>
	<view class="container">
		<view class="top">
			<image src="../../static/backgr.jpg" class="background"></image>
			<view class="box">
				<view class="avatar-box">
					<image :src=" user.avatar " mode="scaleToFill" class="avatar"></image>

				</view>
				<view class="info-box">
					<text class="nickname">{{ user.nickname }} </text>
					<text class="fans">关注 0 | 粉丝 0</text>
					<text class="fans">个人简介:用户很懒,暂时还没有简介~~~</text>
				</view>
			</view>

		</view>

		<view>
			<!-- 选项卡 -->
			<view class="grace-tab" style="margin-top:10px;">
				<scroll-view class="grace-tab-title" :scroll-x="true" :scroll-into-view="titleShowId">
					<view v-for="(tab, index) in tabs" :class="[tabCurrentIndex == index ? 'grace-tab-current' : '']" :id="'tabTag-'+index"
					 @tap="tabChange" :key="index">{{tab.name}}</view>
				</scroll-view>
				<swiper class="grace-tab-swiper" :current="swiperCurrentIndex" @change="swiperChange" style="height:1000upx;">
					<swiper-item>
						<uni-list-item title="基本资料" show-arrow="false" disabled="true"></uni-list-item>
						<uni-list-item title="信息" note="性别 男 20岁 水瓶座 江苏 南京" show-arrow="false"></uni-list-item>
						<uni-list-item title="公司" note="暂无公司" show-arrow="false"></uni-list-item>
						<uni-list-item title="学校" note="南京工业职业技术学院" show-arrow="false"></uni-list-item>
						<uni-list-item title="感情状况" note="未知" show-arrow="false"></uni-list-item>
						<uni-list-item title="注册时间" note="2019-1-1" show-arrow="false"></uni-list-item>
					</swiper-item>
					<swiper-item>动态</swiper-item>
					<swiper-item>更多</swiper-item>
				</swiper>
			</view>
			<!-- 选项卡 -->
		</view>
	</view>
</template>

<script>
	var loginRes, _self;
	import uniGrid from "@dcloudio/uni-ui/lib/uni-grid/uni-grid.vue"
	import uniList from '@dcloudio/uni-ui/lib/uni-list/uni-list.vue';
	import uniListItem from '@dcloudio/uni-ui/lib/uni-list-item/uni-list-item.vue';
	export default {
		components: {
			uniGrid,
			uniList,
			uniListItem
		},
		data() {
			return {
				user: {
					id: 0,
					mobile: '',
					password: '',
					nickname: '',
					token: '',
					status: '',
					regtime: '',
					avatar: '',
					credit:'',
				},
				tabCurrentIndex: 0,
				swiperCurrentIndex: 0,
				tabs: [{
						name: '主页',
						id: 'guanye'
					},
					{
						name: '动态',
						id: 'dongtai'
					},
					{
						name: '更多',
						id: 'gengduo'
					}
				],
				titleShowId: 'tabTag-0'
			};
		},
		onLoad: function(option) {
			uni.setNavigationBarTitle({
				title: '好友个人空间'
			});
			this.user.id = option.fromUId;
		},
		onShow: function() {
			this.getUser();
		},
		methods: {
			getUser: function() {
				uni.request({
					url: this.apiServer + '/user/' + this.user.id,
					method: 'GET',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					success: res => {
						this.user = res.data.data;
					}
				})
			},
			tabChange: function(e) {
				var index = e.target.id.replace('tabTag-', '');
				this.swiperCurrentIndex = index;
				this.tabCurrentIndex = index;
				this.titleShowId = 'tabTag-' + index;
			},
			swiperChange: function(e) {
				var index = e.detail.current;
				this.tabCurrentIndex = index;
				this.titleShowId = 'tabTag-' + index;
			}
		}
	};
</script>

<style scoped>
	.grace-tab-title {
		color: #000000;
	}

	.background {
		width: 100%;
		height: 190px;
	}

	.box {
		position: absolute;
		top: 19px;
		left: 50px;
		color: rgb(255, 255, 255);
	}

	.top {
		display: flex;
		flex-direction: column;
		text-align: center;
		margin-top: 5px;
		height: 190px;
	}



	.info-box {
		display: flex;
		flex-direction: column;
		padding-left: 10px;
		padding-top: 10upx;

	}

	.nickname {
		font-weight: bold;
		font-size: 25px;
	}

	.fans {
		font-size: 16px;
	}
</style>
