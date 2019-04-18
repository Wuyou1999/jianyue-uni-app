<template>
	<view class="container">
		<view class="article-title">{{article.title}}</view>
		<view class="title-time">
			<image :src="article.avatar" class="avatar small"></image>
			<text class="nickname2">{{article.nickname}}</text>
			<view class="kong"></view>
			<view>
				<button v-if="userId != article.uId && !followed" class="butt" @tap="follow">+关注</button>
			</view>
			<view>
				<button v-if="userId != article.uId && followed" class="butt1" @tap="cancelFollow">取消关注</button>
			</view>
		</view>
		<view class="txt">
			<text class="read-text">字数 {{article.content.length}} · 阅读 {{comments.length}}</text>
			<text class="time-text">{{handleTime(article.createTime)}}</text>
		</view>

		<view class="grace-text">
			<rich-text :nodes="article.content" bindtap="tap"></rich-text>
		</view>

		<view>
			<button v-if="!liked" class="butt3" @tap="like">喜欢</button>
		</view>
		<view>
			<button v-if="liked" class="butt4" @tap="cancelLike">取消</button>
		</view>

		<!-- <text class="info-text">评论 ({{ comments.length}})</text> -->

		<view class="grace-accordion-items">
			<view :class="[accordionActive == 'grace-accordion-2' ? 'grace-accordion-title grace-current' : 'grace-accordion-title']"
			 id="grace-accordion-2" @tap="changeAccordion">
				折叠评论({{ comments.length}})
				<text class="grace-list-imgs-arrow grace-iconfont icon-arrow-up" v-if="accordionActive == 'grace-accordion-2'"></text>
				<text class="grace-list-imgs-arrow grace-iconfont icon-arrow-down" v-else></text>
			</view>
			<view class="grace-accordion-body" v-if="accordionActive == 'grace-accordion-2'">
				<view class="grace-list" style='border:none;'>
					<view class="comment-item" v-for="(comment,index) in comments" :key="index">
						<view class="left">
							<image :src="comment.avatar" class="avatar small"></image>
						</view>
						<view class="right">
							<text class="nickname">{{comment.nickname}}</text><br />
							<text>{{comment.content}}</text>
							<view class="lou">
								<text style="margin-right: 10px;">{{comments.length-index}}楼 </text>
								<text>{{comment.commentTime}}</text>
							</view>
						</view>
					</view>
				</view>
			</view>
			<!-- <view class="comment-item" v-for="(comment,index) in comments" :key="index">
			<view class="left">
				<image :src="comment.avatar" class="avatar small"></image>
			</view>
			<view class="right">
				<text class="nickname">{{comment.nickname}}</text><br />
				<text>{{comment.content}}</text>
				<view class="lou">
					<text style="margin-right: 10px;">{{comments.length-index}}楼 </text>
					<text>{{comment.commentTime}}</text>
				</view>
			</view> -->
		</view>
		<input class="uni input comment-box" type="text" placeholder="写下你的评论" v-model="content" required="required" />
		<button class="send" @tap="send">提交</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				article: {
					aId: 0,
					uId: 0,
					title: '',
					content: '',
					avatar: '',
					nickname: '',
					createTime: ''
				},
				comments: [],
				content: '',
				userId: uni.getStorageSync('login_key').userId,
				followed: false,
				liked:false,
				accordionActive: "grace-accordion-1"
			};
		},
		onLoad: function(option) {
				uni.setNavigationBarTitle({
				title: '文章详情'
			});
			//option为object类型，会序列化上个页面传递的参数
			this.article.aId = option.aId;
		},
		onShow: function() {
			this.getArticle();
		},
		onPullDownRefresh: function() {
			this.getArticle();
		},
		methods: {
			changeAccordion: function(e) {
				var accordionIndex = e.currentTarget.id;
				if (this.accordionActive == accordionIndex) {
					accordionIndex = '';
				}
				this.accordionActive = accordionIndex;
			},
			getArticle: function() {
				var _this = this;
				uni.request({
					url: this.apiServer + '/article/' + this.article.aId,
					method: 'GET',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						userId: this.userId
					},
					success: res => {
						// console.log(res.data.data.article);
						_this.article.aId = res.data.data.article.id;
						_this.article.uId = res.data.data.article.uid;
						_this.article.title = res.data.data.article.title;
						_this.article.content = res.data.data.article.content;
						_this.article.nickname = res.data.data.article.nickname;
						_this.article.avatar = res.data.data.article.avatar;
						_this.article.createTime = res.data.data.article.createTime;
						_this.comments = res.data.data.comments;
						if (res.data.data.followed === '已关注') {
							_this.followed = true;
						};
						if (res.data.data.Liked === '已喜欢') {
							_this.liked = true;
						};
						_this.article.createTime = this.handleTime(_this.article.createTime);
						for (var i = 0; i < _this.comments.length; i++) {
							_this.comments[i].commentTime = this.handleTime(_this.comments[i].commentTime);
						}
					},
					complete: function() {
						uni.stopPullDownRefresh();
					}
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
			send: function() {
				console.log('评论人编号：' + this.userId + ',文章编号：' + this.article.aId + '，评论内容：' + this.content);
				uni.request({
					url: this.apiServer + '/comment/add',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						aId: this.article.aId,
						uId: this.userId,
						content: this.content
					},
					success: res => {
						if (res.data.code === 0) {
							uni.showToast({
								title: '评论成功'
							});
							this.getArticle();
							this.content = '';
						}
					}
				});
			},
			follow: function() {
				uni.request({
					url: this.apiServer + '/follow/add',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						fromUId: this.userId,
						toUId: this.article.uId
					},
					success: res => {
						if (res.data.code === 0) {
							uni.showToast({
								title: '关注成功'
							});
							this.followed = true;
						}
					}
				});
			},
			cancelFollow: function() {
				uni.request({
					url: this.apiServer + '/follow/cancel',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						fromUId: this.userId,
						toUId: this.article.uId
					},
					success: res => {
						if (res.data.code === 0) {
							uni.showToast({
								title: '已取消关注'
							});
							this.followed = false;
						}
					}
				});
			},
			like: function() {
				uni.request({
					url: this.apiServer + '/like/add',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						fromUId: this.userId,
						toAId: this.article.aId
					},
					success: res => {
						if (res.data.code === 0) {
							uni.showToast({
								title: '已喜欢'
							});
							this.liked = true;
						}
					}
				});
			},
			cancelLike: function() {
				uni.request({
					url: this.apiServer + '/like/cancel',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						fromUId: this.userId,
						toAId: this.article.aId
					},
					success: res => {
						if (res.data.code === 0) {
							uni.showToast({
								title: '已取消喜欢'
							});
							this.liked = false;
						}
					}
				});
			}
		}
	};
</script>

<style scoped>
	.article-title {
		font-weight: bold;
		font-size: 22px;
		margin-bottom: 18px;
	}

	.title-time {
		display: flex;
		margin-bottom: 10px;
	}

	.title-time image {
		width: 40px;
		height: 40px;
		border-radius: 50%;
	}

	.read-text {
		color: rgb(110, 110, 110);

	}

	.time-text {
		color: rgb(110, 110, 110);
		float: right;
		/* padding-top: 35upx; */
	}

	.txt {
		margin: 10px;
	}

	.comment-item {
		display: flex;
	}

	.right {
		font-size: 16px;
	}

	.grace-text {
		margin-bottom: 10px;
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

	.butt1 {
		background-color: rgb(230, 230, 230);
		color: white;
		height: 70upx;
		width: 190upx;
		display: flex;
		text-align: center;
		justify-content: center;

	}

	.butt3 {
		background-color: rgb(234, 111, 90);
		color: rgb(255, 255, 255);
		height: 75upx;
		width: 190upx;
		display: flex;
		text-align: center;
		justify-content: center;
		border-radius: 30px;
	}

	.butt4 {
		background-color: rgb(255, 255, 255);
		border: 1px solid rgb(234, 111, 90);
		color: rgb(234, 111, 90);
		height: 75upx;
		width: 190upx;
		display: flex;
		text-align: center;
		justify-content: center;
		border-radius: 30px;
	}

	.kong {
		width: 160px;
	}

	.nickname {
		font-weight: bold;
	}

	.nickname2 {
		padding-top: 13upx;
	}

	.lou {
		color: rgb(150, 150, 150);
	}

	.send {
		width: 60%;
		display: flex;
		justify-content: center;
		border-radius: 30px;
		text-align: center;
		background-color: rgb(234, 111, 90);
		color: rgb(255, 255, 255);
		
	}

	.send:hover {
		background-color: rgb(255, 255, 255);
		color: rgb(234, 111, 90);
		border: 1px solid rgb(234, 111, 90);
	}
</style>
