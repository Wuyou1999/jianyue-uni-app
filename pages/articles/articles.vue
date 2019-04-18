<template>
	<view>
		<!-- <text class="article-title">我的文章</text> -->
		<view class="top">
			<view class="top1-left">
				<view class="top1-left-box">
					<view class="box1" v-show="!recommend">
						<navigator @tap="clickshow()">公开文章</navigator>
					</view>
					<view class="box1 navigator" v-show="recommend">
						<navigator>公开文章</navigator>
					</view>
				</view>
				<view class="top1-left-box">
					<view class="box1" v-show="!special">
						<navigator @tap="clickshow2()">私密文章</navigator>
					</view>
					<view class="box1 navigator" v-show="special">
						<navigator>私密文章</navigator>
					</view>
				</view>
			</view>
		</view>
		<view class="middle" v-show="recommend">
			<text>{{articles.length}}篇文章</text>
		</view>
		<view class="grace-scroll-do grace-bg-white grace-common-mt" v-show="recommend">
			<scroll-view class="grace-scroll-x" scroll-x v-for="(item, index) in articles" :scroll-left="scrollIndex == index ? 180 : 0"
			 @touchstart='touchStart' @touchend='touchEnd' :data-id="index" scroll-with-animation="true" :key="index">
				<view class="grace-items">

					<view class="contents">
						<view class="grace-h5 grace-blod"> 第{{index+1}}篇文章 </view>
						<view class="title" @tap="gotoDetail(item.id)">{{item.title}}</view>
						<view class="arttime">{{handleTime(item.createTime)}}</view>
					</view>

				</view>
				<view class="grace-items btn btn-first" :data-id="index" @tap="changStatus" :style="{width : index == deleteIndex ? '0px' : btn1Width + 'px', background:item.status == '已读' ? '#CCCCCC' : '#1AA034', color:item.status == '已读' ? '#999' : '#FFF'}">置顶</view>
				<view class="grace-items btn" :data-id="index" @tap="removeMsg(item.id)" :style="{width : index == deleteIndex ? deleteBtnWidth + 'px' : btn2Width+'px'}">删除</view>
			</scroll-view>
		</view>
		<view class="middle" v-show="special">
			<text>0篇文章</text>
		</view>
		<view class="special" v-show="special">
			<text class="tip1">你的文章空空的哦~</text>
			<image src="../../static/simi.png"></image>
			<button class="write" @tap="gotoWrite">打开编辑器开始写作吧</button>
		</view>
	</view>
</template>

<script>
	var _self, x, y;
	export default {
		data() {

			return {
				storageData: {
					userId: 0,
					nickname: '',
					avatar: '',
					login: false
				},
				/* articles: [], */
				//2个按钮的宽度
				btn1Width: 70,
				btn2Width: 70,
				//2个按钮的总宽 = btn1Width + btn23Width【uni-app 模板内不建议复杂运算请手动配置】
				deleteBtnWidth: 140,
				//正在滑动的索引
				scrollIndex: -1,
				//正在删除的索引
				deleteIndex: -1,
				//模拟数据
				articles: [
					//img - 图片地址 title - 标题 desc - 描述 - status 读取状态
					{
						title: "",
						desc: '',
						status: "未读"
					},
					{
						title: "",
						desc: '',
						status: "未读"
					},
					{
						title: "",
						desc: '',
						status: "未读"
					}
				],
				recommend: true,
				special: false,
			};

		},
		onLoad: function() {
			uni.setNavigationBarTitle({
				title: '我的文章'
			});
			_self = this;
		},
		onShow: function() {
			var _this = this;
			const loginKey = uni.getStorageSync('login_key');
			if (loginKey) {
				this.storageData = {
					login: loginKey.login,
					nickname: loginKey.nickname,
					avatar: loginKey.avatar,
					userId: loginKey.userId
				};
				uni.request({
					url: this.apiServer + '/article/article_list',
					method: 'GET',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						uId: this.storageData.userId
					},
					success: res => {
						_this.articles = res.data.data;
					}
				});
			} else {
				this.storageData = {
					login: false
				};
			}
		},
		methods: {
			clickshow: function() {
				this.recommend = true;
				this.special = false;
			},
			clickshow2: function() {
				this.recommend = false;
				this.special = true;
			},
			gotoDetail: function(aId) {
				uni.navigateTo({
					url: '../article_detail/article_detail?aId=' + aId + '&userId=' + this.storageData.userId
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
			removeMsg: function(aId) {
					uni.showModal({
					title: '提示',
					content: '确认删除吗？',
					cancelText: '再等等',
					cancelColor: '#EA6F5A',
					success: res=> {
						if (res.confirm) {
							uni.request({
								url: this.apiServer + '/article/' + aId,
								method: 'DELETE',
								header: {
									'content-type': 'application/x-www-form-urlencoded'
								},
								success: res => {
									uni.showToast({
										title: '删除成功!',
										duration: 1500
									});
									 this.getArticle(); 
								}
								
							});
						} else if (res.cancel) {
							console.log('用户点击取消')
						};
					}

			   });
			},
			touchStart: function(e) {
				x = e.mp.changedTouches[0].clientX;
				y = e.mp.changedTouches[0].clientY;
			},
			touchEnd: function(e) {
				x = x - e.mp.changedTouches[0].clientX;
				y = y - e.mp.changedTouches[0].clientY;
				if (Math.abs(x) < 50) {
					return;
				}
				if (Math.abs(x) < Math.abs(y)) {
					return;
				}
				var index = e.currentTarget.dataset.id;
				if (index == this.scrollIndex) {
					return;
				}
				_self.scrollIndex = index;
			},
			changStatus: function(e) {
				var index = e.currentTarget.dataset.id;
				this.msg[index].status = "已读";
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
			gotoWrite: function() {
				uni.showModal({
					title: '提示',
					content: '现在要去编写文章吗？',
					cancelColor: '#EA6F5A',
					success: function(res) {
						if (res.confirm) {
							uni.navigateTo({
								url: '../write/write'
							});
						} else if (res.cancel) {
							console.log('用户点击取消')
						};
					}
				});

			},
		}
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

	.box1 {
		font-size: 20px;
	}

	.navigator {
		font-size: 20px;
		color: #fd572b;
		border-bottom: 2px solid #fd572b;
	}

	.article-title {
		display: flex;
		text-align: center;
		justify-content: center;
		font-size: 25px;
		font-weight: bold;
		margin-bottom: 15px;
	}

	.item {
		display: flex;
		flex-direction: column;
		height: 70px;
		border-bottom: 1px solid #eee;

	}

	.num {
		font-size: 18px;
		color: rgb(234, 111, 90);
		font-weight: bold;
	}

	.title {
		font-size: 16px;
	}

	.arttime {
		font-size: 13px;
		color: #6f6f6f;
	}

	.middle {
		padding-left: 15px;
		font-size: 16px;
		color: #6f6f6f;
	}

	.special {
		padding-top: 100px;
		margin: 0 auto;
		width: 90%;
		display: flex;
		justify-content: center;
		text-align: center;
		flex-direction: column;
		height: 300px;
	}

	.tip1 {
		color: #6f6f6f;
		padding-bottom: 10px;
	}

	.special image {
		margin: 0 auto;
		width: 80px;
		height: 90px;

	}

	.write {
		margin-top: 20px;
		width: 60%;
		display: flex;
		justify-content: center;
		border: 1px solid rgb(222, 84, 60);
		color: rgb(222, 84, 60);
		background-color: rgb(255, 255, 255);
	}

	.grace-scroll-x {
		height: 100px;
	}
</style>
