<template>
	<view class="container">
		<view class="ma">
			<image src="../../static/saoma.png" class="sao"></image>
			<view class="kong2"></view>
			<button class="qiandao" @tap="showBanner" :disabled="followed" v-if="storageData.login">签到</button>
			<image src="../../static/yueliang.png" class="yue"></image>
			<text>日间</text>
		</view>
		<graceMaskView :show="show" bgcolor="#FFFFFF" v-on:close="closeBanner">
			<view>
				<image src="../../static/qiandao.jpg" style="width:100%; margin-top:25px; border-top-right-radius:5px; border-top-left-radius:5px;"
				 mode="widthFix"></image>
			</view>
			<view style="padding:25px; padding-bottom:30px;">
				<button type='warn' style="background:#F6644D; padding:0 20px;" @tap="closeBanner">我知道了</button>
			</view>
		</graceMaskView>
		<view class="top">
			<!-- <view class="welcome" v-if="!storageData.login">
				<image src="../../static/lihua.png"></image>
				<text>欢迎使用!</text>
			</view>
			<view class="avatar-box" v-if="!storageData.login">
				<image src="../../static/default.png" mode="scaleToFill" class="avatar"></image>
			</view> -->
			<view class="avatar-box1" v-if="!storageData.login">
				<image src="../../static/default.png" mode="scaleToFill" class="avatar"></image>
				<view class="info-box1" v-if="!storageData.login">
					<text class="nickname" @tap="Signin">点击登录</text>
					<text class="fans">立即领取简阅福利</text>
				</view>
			</view>

			<view class="avatar-box1" v-if="storageData.login">
				<image :src="storageData.avatar" mode="scaleToFill" class="avatar" @tap="gotoSpace(storageData.userId)"></image>
				<view class="info-box1" v-if="storageData.login">
					<text class="nickname">{{ storageData.nickname }} </text>
					<text class="fans">关注 {{follows.length}} 粉丝 {{fans.length}}</text>
				</view>
			</view>

			<!-- <view class="info-box" v-if="!storageData.login">
				<navigator url="../signin/signin">
					<button class="login">手机号登录</button></navigator>
				</navigator>
			</view>
			<text class="login_tip" v-if="!storageData.login">切换账号 | 登录遇到问题</text> -->
		</view>
		<view>
			<uni-list-item title="简阅钻: 0.0" thumb="http://wuyou-soft.oss-cn-hangzhou.aliyuncs.com/%E6%98%9F%E6%98%9F.png"></uni-list-item>
		</view>
		<view class="middle">
			<view class="item it">
				<image src="../../static/wenzhang.png"></image>
				<text class="My" @tap="article">我的文章</text>
				<text class="tx" v-if="!storageData.login">0篇文章</text>
				<text class="tx" v-if="storageData.login">{{articles.length}}篇文章</text>
			</view>
			<view class="item it">
				<image src="../../static/guanzhu.png"></image>
				<text class="My" @tap="follow">我的关注</text>
				<text class="tx" v-if="!storageData.login">0</text>
				<text class="tx" v-if="storageData.login">{{follows.length}}</text>
			</view>
			<view class="item it">
				<image src="../../static/xiaoxi.png"></image>
				<text class="My">我的消息</text>
				<text class="tx">{{messnum}}</text>
			</view>
			<view class="item it">
				<image src="../../static/jifen.png"></image>
				<text class="My">我的积分</text>
				<text class="tx" v-if="!storageData.login">{{scores}}</text>
				<text class="tx" v-if="storageData.login"> {{follows.length*3+articles.length*5+likes.length*2+
				fans.length*2}} </text>
			</view>

		</view>

		<swiper class="grace-swiper" autoplay="true" indicator-dots indicator-color="rgba(255, 255, 255, 1)"
		 indicator-active-color="#00B26A" style="height :240upx " interval="3000">
			<swiper-item v-for="(item, index) in swiperItems" :key="index">
				<navigator :url='item.path' :open-type="item.openType">
					<image :src='item.imgUrl' mode='widthFix' class="MySwiper"></image>
					<view class="title">{{item.title}}</view>
				</navigator>
			</swiper-item>
		</swiper>


		<view class="bellow">
			<uni-list-item title="简阅会员" note="限时赠送会员" thumb="https://wuyou-soft.oss-cn-hangzhou.aliyuncs.com/weibiaoti-.png"></uni-list-item>
			<uni-list-item title="简阅活动" note="万元奖金等着你" thumb="https://wuyou-soft.oss-cn-hangzhou.aliyuncs.com/timg.jpg"></uni-list-item>
			<uni-list-item title="我的钱包" note="0张优惠券" thumb="http://wuyou-soft.oss-cn-hangzhou.aliyuncs.com/%E9%92%B1%E5%8C%85.png"></uni-list-item>
			<navigator url="../articles/articles" class="article">
				<uni-list-item title="我的专题/文集" thumb="http://wuyou-soft.oss-cn-hangzhou.aliyuncs.com/%E4%B9%A6%E6%9E%B6%20%282%29.png"></uni-list-item>
			</navigator>
			<view class="kong1"></view>
			<navigator url="../setting/setting">
			<uni-list-item title="设置" thumb="http://wuyou-soft.oss-cn-hangzhou.aliyuncs.com/shezhi.png"></uni-list-item>
			</navigator>
			<uni-list-item title="帮助与反馈" thumb="https://wuyou-soft.oss-cn-hangzhou.aliyuncs.com/timg%20%281%29.jpg"></uni-list-item>
		</view>
	</view>
</template>

<script>
	var loginRes, _self;
	import graceMaskView from "../../graceUI/components/graceMaskView.vue";
	import uniGrid from "@dcloudio/uni-ui/lib/uni-grid/uni-grid.vue"
	import uniList from '@dcloudio/uni-ui/lib/uni-list/uni-list.vue';
	import uniListItem from '@dcloudio/uni-ui/lib/uni-list-item/uni-list-item.vue';
	export default {
		components: {
			uniGrid,
			uniList,
			uniListItem,
			graceMaskView
		},
		data() {
			return {
				staticUrl: this.staticUrl,
				show: false,
				followed: false,
				storageData: {},
				articlenum: '0',
				attennum: '0',
				messnum: '0',
				scores: '0',
				swiperItems: [{
						"imgUrl": "../../static/read1.jpg",
						"path": "../index/index",
						"title": "简阅",
						"openType": "navigate"
					},
					{
						"imgUrl": "../../static/read2.jpg",
						"path": "../index/index",
						"title": "简阅",
						"openType": "switchTab"
					},
					{
						"imgUrl": "../../static/read3.jpg",
						"path": "../index/index",
						"title": "简阅",
						"openType": "switchTab"
					},
					{
						"imgUrl": "../../static/read4.jpg",
						"path": "../index/index",
						"title": "简阅",
						"openType": "switchTab"
					}
				],
				follows: [],
				fans: [],
				articles: [],
				likes: [],
				credit: uni.getStorageSync('login_key').credit
			};
		},
		onLoad: function() {
			/* 	_self = this;
				uni.request({
					url: 'https://www.easy-mock.com/mock/5bb833775df5622d84ac87ca/example/swiperwithtitle#!method=get',
					success: function(res) {
						console.log(res);
						_self.swiperItems = res.data.data;
					}
				}); */
		},
		onShow: function() {
			var _this = this;
			const loginKey = uni.getStorageSync('login_key');
			console.log("come");
			if (loginKey) {
				console.log(loginKey);
				this.storageData = {
					login: loginKey.login,
					nickname: loginKey.nickname,
					avatar: loginKey.avatar,
					userId: loginKey.userId,
					credit: loginKey.credit
				};
			} else {
				this.storageData = {
					login: false
				};
			};
			this.getFollows();
			this.getFans();
			this.getArticle();
			this.getlikes();
		},
		methods: {
			// 第1个演示 开启与关闭
			showBanner: function() {
				if (uni.getStorageSync('login_key').login === true) {
					this.show = true;
					uni.showToast({
						title: '签到成功,积分+2',
						duration: 2500
					});
					this.followed = true;
				} else {
					uni.showModal({
						title: '提示',
						content: '对不起您还未登录!',
						cancelText: '再等等',
						cancelColor: '#EA6F5A',
						success: function(res) {
							if (res.confirm) {
								uni.navigateTo({
									url: '../signin/signin'
								});
							} else if (res.cancel) {
								console.log('用户点击取消')
							};
						}
					});
				}
			},
			closeBanner: function() {
				this.show = false;
			},
			follow: function() {
				if (uni.getStorageSync('login_key').login === true) {
					uni.navigateTo({
						url: 'follow'
					});
				} else {
					uni.showModal({
						title: '提示',
						content: '对不起您还未登录!',
						cancelText: '再等等',
						cancelColor: '#EA6F5A',
						success: function(res) {
							if (res.confirm) {
								uni.navigateTo({
									url: '../signin/signin'
								});
							} else if (res.cancel) {
								console.log('用户点击取消')
							};
						}
					});
				}
			},
			article: function() {
				if (uni.getStorageSync('login_key').login === true) {
					uni.navigateTo({
						url: '../articles/articles'
					});
				} else {
					uni.showModal({
						title: '提示',
						content: '对不起您还未登录!',
						cancelText: '再等等',
						cancelColor: '#EA6F5A',
						success: function(res) {
							if (res.confirm) {
								uni.navigateTo({
									url: '../signin/signin'
								});
							} else if (res.cancel) {
								console.log('用户点击取消')
							};
						}
					});
				}

			},
			/* setting: function() {
				if (uni.getStorageSync('login_key').login === true) {
					uni.navigateTo({
						url: '../setting/setting'
					});
				} else {
					uni.showModal({
						title: '提示',
						content: '对不起您还未登录!',
						cancelText: '再等等',
						cancelColor: '#EA6F5A',
						success: function(res) {
							if (res.confirm) {
								uni.navigateTo({
									url: '../signin/signin'
								});
							} else if (res.cancel) {
								console.log('用户点击取消')
							};
						}
					});
				}

			}, */
			Signin: function() {
				uni.navigateTo({
					url: '../signin/signin'
				});
			},
			getFollows: function() {
				var _this = this;
				uni.request({
					url: this.apiServer + '/follow/followlist?fromUId=' + uni.getStorageSync('login_key').userId,
					method: 'GET',
					header: {
						'content-type': 'application/json'
					},
					success: res => {
						_this.follows = res.data.data;
					}
				});
			},
			getFans: function() {
				var _this = this;
				uni.request({
					url: this.apiServer + '/follow/fanlist?toUId=' + uni.getStorageSync('login_key').userId,
					method: 'GET',
					header: {
						'content-type': 'application/json'
					},
					success: res => {
						_this.fans = res.data.data;
					}
				});
			},
			getArticle: function() {
				var _this = this;
				uni.request({
					url: this.apiServer + '/article/article_list',
					method: 'GET',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						uId: uni.getStorageSync('login_key').userId
					},
					success: res => {
						_this.articles = res.data.data;
					}
				});
			},
			getlikes: function() {
				var _this = this;
				uni.request({
					url: this.apiServer + '/like/likelist?fromUId=' + uni.getStorageSync('login_key').userId,
					method: 'GET',
					header: {
						'content-type': 'application/json'
					},
					success: res => {
						_this.likes = res.data.data;
					}
				});
			},
			gotoSpace: function(userId) {
				uni.navigateTo({
					url: 'myspace?userId=' + userId
				});
			},
		}
	};
</script>

<style scoped>
	/* 遮罩层 */
	.uni-mask {
		background: rgba(0, 0, 0, 0.5);
		position: fixed;
		width: 100%;
		height: 100%;
		left: 0;
		top: 0;
		z-index: 1;
	}

	/* 弹出层形式的广告 */
	.uni-banner {
		width: 70%;
		position: fixed;
		left: 50%;
		top: 50%;
		background: #FFF;
		border-radius: 10upx;
		z-index: 99;
		transform: translate(-50%, -50%);
	}

	.sao {
		padding-left: 10px;
	}

	.ma {
		display: flex;
		height: 30px;
	}

	.qiandao {
		height: 30px;
		font-size: 14px;
		position: absolute;
		right: 90px;
		background-color: #de533a;
		background: linear-gradient(40deg, #ffd86f, #fc6262);
		color: rgb(255, 255, 255);
	}

	.ma image {
		height: 25px;
		width: 25px;
	}

	.grace-swiper {
		margin: 0 auto;
		width: 95%;

	}

	.kong2 {
		width: 290px;
	}

	.welcome {
		display: flex;
		justify-content: center;
		margin-bottom: 50px;
		text-align: center;
		color: rgb(168, 168, 168);
		font-size: 23px;
	}

	.welcome image {
		width: 35px;
		height: 35px;
		border-radius: 50%;
	}

	.login {
		margin-left: 15px;
		margin-top: 50px;
		width: 280px;
		height: 50px;
		border-radius: 30px;
		outline: none;
		background-color: rgb(222, 84, 60);
		color: #FFFFFF;
	}

	.login_tip {
		color: rgb(73, 105, 153);
		margin-top: 110px;
		display: flex;
		text-align: center;
		justify-content: center;
		font-size: 16px;
	}


	.top {
		display: flex;
		height: 70px;
		margin-top: 10px;
		flex-direction: column;
	}

	.avatar-box {
		display: flex;
		/* align-items: center; */
		justify-content: center;
		margin-bottom: 35px;
	}

	.avatar-box1 {
		display: flex;
		/* align-items: center; */
		/* float: left; */
		margin-bottom: 35px;
		padding-left: 20upx;
	}

	.info-box {
		display: flex;
		/* align-items: center; */
		justify-content: center;
	}

	.info-box1 {
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
		color: rgb(153, 153, 153);
		font-size: 16px;
	}

	.setting {
		color: rgb(224, 96, 73);

	}

	.middle {
		padding-top: 20px;
		display: flex;
		width: 95%;
		height: 175upx;
		margin: 0 auto;
		margin-bottom: 27upx;
		border-bottom: 1px solid #eee;
		border-radius: 10px;
		box-shadow: 1px 2px 10px #eee;
	}

	.it {
		display: flex;
		flex-direction: column;
		/* border:  #eee; */
		width: 25%;
		text-align: center;
		line-height: 35upx;
	}

	.it image {
		width: 35px;
		height: 35px;
		padding-left: 29px;
		padding-bottom: 8px;
	}

	.My {
		padding-bottom: 10upx;
		text-align: center;
	}

	.tx {
		font-size: 14px;
		/* font-weight: bold; */
		color: rgb(153, 153, 153);
	}

	.kong1 {
		height: 28upx;
		background-color: #eee;
	}

	.MySwiper {
		border-radius: 15px;
	}
</style>
