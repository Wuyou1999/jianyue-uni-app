<template>
	<view class="container">
		<view class="top">
			<view class="welcome" v-if="!storageData.login">
				<image src="../../static/lihua.png"></image>
				<text>欢迎使用</text>
			</view>

			<view class="avatar-box">
				<image src="../../static/default.png" mode="scaleToFill" class="avatar" v-if="!storageData.login">
				</image>
				<image :src="storageData.avatar" mode="scaleToFill" class="avatar" v-if="storageData.login"></image>
			</view>
			<view class="info-box">
				<navigator url="../signin/signin" v-if="!storageData.login">
					<button class="login">登录</button></navigator>
				<text v-if="storageData.login" class="nickname">{{ storageData.nickname }} </text>
				<text class="kong"></text>
				<navigator url="../setting/setting" v-if="storageData.login" class="setting">个人设置</navigator>
				<!-- <button @tap="signOut" class="setting">个人设置</button> -->
				</navigator>
			</view>
		</view>
		<view class="middle" v-if="storageData.login">
			<view class="item it">
				<navigator url="../articles/articles" class="article">
					<image src="../../static/wenzhang.png"></image>

				</navigator>
				<text>文章 · <text class="tx">{{articlenum}}</text><br /></text>
			</view>
			<view class="item it">
				<image src="../../static/guanzhu.png"></image>
				<text>关注 · <text class="tx">{{attennum}}</text><br /></text>
			</view>
			<view class="item it">
				<image src="../../static/xiaoxi.png"></image>
				<text>消息 · <text class="tx">{{messnum}}</text><br /></text>
			</view>
			<view class="item it">
				<image src="../../static/jifen.png"></image>
				<text>积分 · <text class="tx">{{scores}}</text><br /></text>
			</view>
			<!-- <uni-grid :options="[
    {image:'../../static/wenzhang.png',text:'文章 · 22'},
    {image:'../../static/guanzhu.png',text:'关注 · 20'},
    {image:'../../static/xiaoxi.png',text:'消息 · 99+'},
    {image:'../../static/jifen.png',text:'积分 · 100'}]"
			 column-num="4">
			</uni-grid> -->
		</view>
		<view class="bellow" v-if="storageData.login">
			<!-- <uni-list>
				<uni-list-item :title="article.title" v-for="(article, index) in articles" :key="index" thumb="https://upload-images.jianshu.io/upload_images/16150151-477ce947c1d1f48c.jpeg?imageMogr2/auto-orient/strip|imageView2/1/w/360/h/240"
				 :note="article.content"></uni-list-item>
			</uni-list> -->
          <uni-list-item title="简阅会员" note="限时赠送会员"></uni-list-item>
		  <uni-list-item title="简阅活动" note="万元奖金等着你"></uni-list-item>
		  <uni-list-item title="我的钱包" note="0张优惠券"></uni-list-item>
		  <uni-list-item title="我的专题/文集" ></uni-list-item>
		  <view class="kong1"></view>
		  <navigator url="../setting/setting" >
		  	<uni-list-item title="设置" ></uni-list-item>
		  </navigator>
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
				storageData: {},
				articlenum: '5',
				attennum: '1w',
				messnum: '99+',
				scores: '10w',
				/* articles: [{
						id: 1,
						title: '第一篇文章',
						content: '浅行于红尘烟雨中，只因一个不经意的眼神，便刻骨铭心'
					},
					{
						id: 2,
						title: '第二篇文章',
						content: '浅行于红尘烟雨中，只因一个不经意的眼神，便刻骨铭心'
					},
					{
						id: 3,
						title: '第三篇文章',
						content: '浅行于红尘烟雨中，只因一个不经意的眼神，便刻骨铭心'
					},
					{
						id: 4,
						title: '第四篇文章',
						content: '浅行于红尘烟雨中，只因一个不经意的眼神，便刻骨铭心'
					},
					{
						id: 5,
						title: '第五篇文章',
						content: '浅行于红尘烟雨中，只因一个不经意的眼神，便刻骨铭心'
					}

				] */
			};
		},
		onLoad: function() {},
		onShow: function() {
			var _this = this;
			const loginKey = uni.getStorageSync('login_key');
			console.log("come");
			if (loginKey) {
				console.log(loginKey);
				this.storageData = {
					login: loginKey.login,
					nickname: loginKey.nickname,
					avatar: loginKey.avatar
				};
			} else {
				this.storageData = {
					login: false
				};
			}

		},
		methods: {

		}
	};
</script>

<style scoped>
	.welcome {
		display: flex;
		justify-content: center;
		margin-bottom: 50px;
		text-align: center;
	}

	.welcome image {
		width: 35px;
		height: 35px;
		border-radius: 50%;
	}

	.login {
		margin-left: 15px;
		margin-top: 50px;
		width: 200px;
		height: 50px;
		border-radius: 30px;
		outline: none;
		background-color: rgb(222, 84, 60);
		color: #FFFFFF;
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

	.info-box {
		display: flex;
		/* align-items: center; */
		justify-content: center;
	}

	.setting {
		color: rgb(224, 96, 73);

	}

	.middle {
		margin-top: 20px;
		display: flex;
		width: 100%;
		height: 150upx;
		margin-bottom: 27upx;
	}

	.it {
		display: flex;
		flex-direction: column;
		/* border:  #eee; */

		width: 25%;
		text-align: center;
		line-height: 40upx;
	}

	.it image {
		width: 40px;
		height: 40px;
		padding-left: 19px;
		padding-bottom: 8px;
	}

	.tx {
		font-size: 20px;
		/* font-weight: bold; */
		color: rgb(62, 175, 14);
	}

	.kong {
		width: 50upx;
	}
	.kong1{
		height: 50upx;
		background-color:#eee;
	}
</style>
