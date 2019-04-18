<template>
	<view class="container">
		<view class="top">
			<view class="top1-left">
				<view class="top1-left-box">
					<view class="box1" v-show="!recommend">
						<navigator @tap="clickshow()">关注</navigator>
					</view>
					<view class="box1 navigator" v-show="recommend">
						<navigator>关注</navigator>
					</view>
				</view>
				<view class="top1-left-box">
					<view class="box1" v-show="!special">
						<navigator @tap="clickshow2()">粉丝</navigator>
					</view>
					<view class="box1 navigator" v-show="special">
						<navigator>粉丝</navigator>
					</view>
				</view>
				<view class="top1-left-box">
					<view class="box1" v-show="!serialize">
						<navigator @tap="clickshow3()">喜欢</navigator>
					</view>
					<view class="box1 navigator" v-show="serialize">
						<navigator>喜欢</navigator>
					</view>
				</view>
			</view>
		</view>
		<view class="follow" v-for="(follow, index) in follows" :key="index" v-show="recommend">
			<view class="bellow">
				<view class="item">
					<image :src="follow.avatar" class="avat" @tap="gotoFriend(follow.toUId)"></image>
					<text class="nickname">{{follow.nickname}}</text>
                   <!-- <view>
                   	<button v-if="followed" class="butt1" @tap="cancelFollow">取消关注</button>
                   </view> -->
				</view>
			</view>
		</view>
		<view class="follow" v-for="(fan, index1) in fans" :key="index1" v-show="special">
			<view class="bellow">
				<view class="item">
					<image :src="fan.avatar" class="avat"  @tap="gotoFan(fan.fromUId)"></image>
					<text class="nickname">{{fan.nickname}}</text>
				</view>

			</view>
		</view>
		<view class="follow" v-for="(like, index2) in likes" :key="index2" v-show="serialize">
			<view class="bellow">
				<view class="item">
					<text class="nickname" @tap="gotoDetail(like.toAId)">{{like.title}}</text>
					<text class="arttime">{{handleTime(like.createTime)}}</text>
				</view>

			</view>
		</view>
	</view>
</template>

<script>
	var _self;
	export default {
		data() {
			return {
				follows: {
					avatar: '',
					nickname: '',
					toUId: ''
				},
				fans: {
					uId: 0,
					avatar: '',
					nickname: '',
					fromUId: ''
				},
				/* fans: [], */
				/* follows: [], */
				likes: [],
				userId: uni.getStorageSync('login_key').userId,
				followed: true,
				recommend: true,
				special: false,
				serialize: false
			}
		},
		onLoad: function() {
			_self = this;
				uni.setNavigationBarTitle({
				title: '我的关注'
			});
		},
		onShow: function() {
			this.getFollows();
			this.getFans();
			this.getlikes();
		},
		methods: {
			clickshow: function() {
				this.recommend = true;
				this.special = false;
				this.serialize = false;
			},
			clickshow2: function() {
				this.recommend = false;
				this.special = true;
				this.serialize = false;
			},
			clickshow3: function() {
				this.recommend = false;
				this.special = false;
				this.serialize = true;
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
			gotoDetail: function(aId) {
				uni.navigateTo({
					url: '../article_detail/article_detail?aId=' + aId
				});

			},
			handleTime: function(date) {
				var d = new Date(date);
				var year = d.getFullYear();
				var month = d.getMonth() + 1;
				var day = d.getDate() < 10 ? '0' + d.getDate() : '' + d.getDate();
				var hour = d.getHours() < 10 ? '0' + d.getHours() : '' + d.getHours();
				var minutes = d.getMinutes() < 10 ? '0' + d.getMinutes() : '' + d.getMinutes();
				var seconds = d.getSeconds() < 10 ? '0' + d.getSeconds() : '' + d.getSeconds();
				return year + '-' + month + '-' + day + ' ' + hour + ':' + minutes + ':' + seconds;
			},
			cancelFollow: function() {
				uni.request({
					url: this.apiServer + '/follow/cancel',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						fromUId: uni.getStorageSync('login_key').userId,
						toUId: this.follows.toUId,
					},
					success: res => {
						if (res.data.code === 0) {
							uni.showToast({
								title: '已取消关注'
							});
							this.getFollows();
						}
					}
				});
			},
			gotoFriend: function(toUId){
				uni.navigateTo({
					url: 'friend?toUId='+toUId 
				});
			},
			gotoFan: function(fromUId){
				uni.navigateTo({
					url: 'fan?fromUId='+fromUId 
				});
			}

		},

	}
</script>

<style scoped>
	.top {
		width: 100%;
		height: 35px;
		background: #ffffff;
		display: flex;
		justify-content: center;
		border-bottom: 1px solid #eee;
		padding: 10px;
	}

	.top1-left {
		margin-left: 3px;
		display: flex;
		width: 50%;
		/* border: 1px solid #007AFF; */
	}

	.top1-left-box {
		height: 100%;
		display: flex;
		flex: 1 1 30%;
	}

	.box1 {
		font-size: 20px;
	}

	.navigator {
		font-size: 20px;
		color: #fd572b;
		border-bottom: 2px solid #fd572b;
	}

	.bellow {
		display: flex;
	}

	.bellow image {
		width: 40px;
		height: 40px;
		border-radius: 50%;
	}

	.butt {
		background-color: #00C777;
		color: white;
		height: 70upx;
		width: 190upx;
		display: flex;
		text-align: center;
		justify-content: center;

	}

	.nickname {
		padding-top: 20px;
	}

	.avat {
		padding-top: 12px;
	}

	.butt1 {
		background-color: rgb(255, 255, 255);
		color: rgb(150, 150, 150);
		height: 70upx;
		width: 190upx;
		display: flex;
		text-align: center;
		justify-content: center;

	}

	.button {
		margin-left: 130px;
		padding-top: 20upx;
	}

	.item {
		display: flex;
		height: 70px;
		border-bottom: 1px solid #eee;
		margin: 0 auto;
		width: 95%;
		border-bottom: 1px solid #eee;
		font-size: 18px;
	}

	.arttime {
		position: absolute;
		right: 10px;
		font-size: 17px;
		padding-top: 20px;
		color: #6f6f6f;
	}

	.likeitem {
		height: 70px;
		margin: 0 auto;
		width: 95%;
		border-bottom: 1px solid #eee;
	}
</style>
