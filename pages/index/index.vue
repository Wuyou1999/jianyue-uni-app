<template>
	<view class="container">
		<view class="top1">
			<view class="top1-left">
				<view class="top1-left-box">
					<view class="box1" v-show="!recommend">
						<navigator @tap="clickshow()">推荐</navigator>
					</view>
					<view class="box1 navigator" v-show="recommend">
						<navigator>推荐</navigator>
					</view>
				</view>
				<view class="top1-left-box">
					<view class="box1" v-show="!special">
						<navigator @tap="clickshow2()">专题</navigator>
					</view>
					<view class="box1 navigator" v-show="special">
						<navigator>专题</navigator>
					</view>
				</view>
				<view class="top1-left-box">
					<view class="box1" v-show="!serialize">
						<navigator @tap="clickshow3()">连载</navigator>
					</view>
					<view class="box1 navigator" v-show="serialize">
						<navigator>连载</navigator>
					</view>
				</view>
			</view>
		</view>
		<view class="article-box">
			<view class="article" v-for="(article, index) in articles" :key="index" v-show="recommend">
				<!-- 大于等于三张图片的显示方式 -->
				<view class="box" v-if="article.imgs.length >= 3">
					<!-- 标题 -->
					<text class="article-title" @tap="gotoDetail(article.id)">{{ article.title }}</text>
					<view class="thumbnail-box">
						<view class="thumbnail-item" v-for="(item, index1) in article.imgs" :key="index1" v-if="index1 < 3">
							<image :src="item.imgUrl"></image>
						</view>
					</view>
				</view>
				<!-- 小于三张图片的显示方式 -->
				<view class="box" v-else-if="article.imgs.length >= 1">
					<view class="text-img-box">
						<view class="top">
							<text class="article-title" @tap="gotoDetail(article.id)">{{ article.title }}</text>
						</view>
						<view class="left-right">
							<view class="left">
								<rich-text :nodes="article.content" bindtap="tap"></rich-text>
							</view>
							<view class="right">
								<image :src="article.imgs[article.imgs.length - 1].imgUrl"></image>
							</view>
						</view>

					</view>
				</view>
				<!-- 没用图片的显示方式 -->
				<view class="box" v-else>
					<text class="article-title" @tap="gotoDetail(article.id)">{{ article.title }}</text><br />
					<view class="content1">
						<rich-text :nodes="article.content" bindtap="tap"></rich-text>
					</view>
				</view>
				<!-- 文章作者等信息 -->
				<view class="title-time">

					<image :src="article.avatar" class="avatar_t"></image>
					<text class="name-text">{{ article.nickname }}</text>
					<view class="cl">
						<image src="../../static/pl.png" class="pl"></image>
						<text>{{comments.length}}</text>
						<view class="kong1"></view>
						<image src="../../static/xihuan.png" class="pl"></image>
						<text>{{likes.length}}</text>
						<text class="time-text">{{ (article.createTime) }}</text>
					</view>

				</view>
				<view class="kong"></view>
			</view>
		</view>

		<view class="btn">
			<button class="circle-btn" @tap="gotoWrite"><text class="icon-text">+</text></button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				article: {
					aId: 0,
					title: '',
					content: '',
					nickname: '',
					createTime: ''
				},
				comments: [],
				articles: [],
				likes: [],
				recommend: true,
				special: false,
				serialize: false
			};
		},
		onLoad: function() {

		},
		onShow: function() {
			this.getArticles();
		},
		onPullDownRefresh: function() {
			this.getArticles();
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
			getArticles: function() {
				var _this = this;
				uni.request({
					url: this.apiServer + '/article/list',
					method: 'GET',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					success: res => {
						_this.articles = res.data.data;
						for (var i = 0; i < _this.articles.length; i++) {
							_this.articles[i].createTime = this.handleTime(_this.articles[i].createTime);
							_this.articles[i].content = this.handleContent(_this.articles[i].content);
						}
					},
					complete: function() {
						uni.stopPullDownRefresh();
					}
				});
			},
			gotoDetail: function(aId) {
				if (uni.getStorageSync('login_key').login === true) {
					uni.navigateTo({
						url: '../article_detail/article_detail?aId=' + aId
					});
				} else {
					uni.showModal({
						title: '请登录',
						content: '未登录不可查看文章详情',
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
			handleTime: function(date) {
				var d = new Date(date);
				var year = d.getFullYear();
				var month = d.getMonth() + 1;
				var day = d.getDate() < 10 ? '0' + d.getDate() : '' + d.getDate();
				var hour = d.getHours() < 10 ? '0' + d.getHours() : '' + d.getHours();
				var minutes = d.getMinutes() < 10 ? '0' + d.getMinutes() : '' + d.getMinutes();
				var seconds = d.getSeconds() < 10 ? '0' + d.getSeconds() : '' + d.getSeconds();
				return year + '-' + month + '-' + day + ' ' + hour + ":" + minutes + ':' + seconds
			},
			handleContent: function(description) {
				description = description.replace(/(\n)/g, "");
				description = description.replace(/(\t)/g, "");
				description = description.replace(/(\r)/g, "");
				description = description.replace(/<\/?[^>]*>/g, "");
				description = description.replace(/\s*/g, "");
				return description;
			},
			gotoWrite: function() {
				if (uni.getStorageSync('login_key').login === true) {
					uni.navigateTo({
						url: '../write/write'
					});
				} else {
					uni.showModal({
						title: '请登录',
						content: '未登录不可编写文章',
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
			}
		}
	}
</script>

<style scoped>
	.top1 {
		width: 100%;
		height: 35px;
		background: #ffffff;
		display: flex;
		justify-content: space-between;
		border-bottom: 1px solid #aaa;
		/* border: 1px solid #00B26A; */
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

	.navigator {
		color: #fd572b;
		border-bottom: 2px solid #fd572b;
	}

	.icon-text {
		font-size: 20pt;
		color: #fff;
	}

	.circle-btn {
		position: absolute;
		bottom: 65px;
		right: 15px;
		position: fixed;
		width: 58px;
		height: 58px;
		border-radius: 50%;
		background-color: #de533a;
		background: linear-gradient(40deg, #ffd86f, #fc6262);
		box-shadow: 2px 5px 10px #aaa;
		cursor: pointer;
		border: none;
		outline: none;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.thumbnail-box {
		display: flex;
		height: 95px;
	}

	.thumbnail-item {
		flex: 1 1 33.3%;
	}

	.thumbnail-item image {
		width: 100%;
		height: 100%;
		border-radius: 10px;
	}

	.article-title {
		font-weight: bold;
		font-size: 22px;
	}

	.title-time {
		margin-bottom: 10px;
		margin: 0 auto;
		display: flex;
		height: 40px;
	}

	.avatar_t {
		width: 28px;
		height: 28px;
		border-radius: 50%;
	}

	.pl {
		width: 20px;
		height: 20px;
	}

	.cl {
		margin-left: 40px;
		display: flex;
		padding-top: 8px;
	}

	.time-text {
		color: rgb(110, 110, 110);
		padding-top: 20upx;
		position: absolute;
		right: 13px;
		padding-top: 1px;
	}

	.left-right {
		display: flex;
		height: 95px;
	}

	.left {
		flex: 1 1 67%;
		font-size: 16px;
		overflow: hidden;
		-web-line-clamp: 3;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-box-orient: vertical;
		line-height: 23px;
	}

	.right {
		flex: 1 1 33%;
	}

	.left-right image {
		width: 100%;
		height: 100%;
		border-radius: 10px;
	}

	.box {
		border-top: 1px solid #eee
	}

	.kong {
		height: 20upx;
		background-color: #eee;
	}

	.content1 {
		font-size: 16px;
		height: 95px;
		overflow: hidden;
		-web-line-clamp: 3;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-box-orient: vertical;
		line-height: 23px;
	}

	.kong1 {
		width: 20px;
	}

	.name-text {
		padding-top: 5px;
	}
</style>
