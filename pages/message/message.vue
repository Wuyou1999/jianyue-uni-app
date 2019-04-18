<template>
	<view class="container">
		<!-- <view class="container" v-show="follow">
			<h3>消息显示</h3>
			<ul>
				<li v-for="(message, index) in messages" :key="index">
					{{message}}
				</li>
			</ul>
		</view> -->

		<h3>请输入您要发送的内容:</h3>
		<input type="text" class="item1" v-model="sendMsg" />
		<button type="primary" @tap="showBanner">发送</button>

		<graceMaskView :show="show" bgcolor="#FFFFFF" v-on:close="closeBanner">
			<view>
				<text class="msg1">您有{{messages.length-1}}条新消息</text>
				<text v-for="(message, index) in messages" :key="index" class="msg">
					{{message}}
				</text>
			</view>
			<view style="padding:25px; padding-bottom:30px;">
				<button type='warn' style="background:#F6644D; padding:0 20px;" @tap="closeBanner">我知道了</button>
			</view>
		</graceMaskView>
	</view>
</template>

<script>
	var socket;
	//对应下面3个标签的新闻内容数据
	import graceMaskView from "../../graceUI/components/graceMaskView.vue";
	export default {
		data() {
			return {
				messages: [],
				sendMsg: '',
				follow: false,
				show: false
			};
		},
		onLoad: function() {
			var _this = this;
			//创建WebSocket对象，指定要连接的服务器地址和端口，建立连接
			socket = new WebSocket("ws://10.40.250.164:8080/websocket");
			//打开连接
			socket.onopen = function() {
				console.log("Socket已打开");

			};
			//获得服务端推送的消息
			socket.onmessage = function(msg) {
				console.log(msg.data);
				_this.messages.push(msg.data);
				console.log(_this.messages);
			};
			//关闭连接
			socket.onclose = function() {
				console.log("Socket已关闭");
			};
			//发送错误
			/* socket.onerror = function() {
				alert("Socket发生了错误");
			} */
		},
		watch: {
			// 如果 `messages` 发生改变，这个函数就会运行
			messages: function(newMsg, oldMsg) {
				this.messages = newMsg;
			},
		},
		methods: {
			showBanner: function() {
				if (this.sendMsg === "") {
					uni.showModal({
						title: '提示',
						content: '当前内容为空',
						confirmText: '确定',

					});
				} else {
					socket.send(this.sendMsg);
					this.show = true;
					uni.showToast({
						title: '发送成功！',
						duration: 2500
					});
				}

			},
			closeBanner: function() {
				this.show = false;
			}
		},
		components: {
			graceMaskView
		}
	};
</script>

<style scoped>
	.item1{
		height:35px;
		border-bottom: 1px solid #eee;
	}
	.msg1 {
		display: flex;
		text-align: center;
		justify-content: center;
		color: rgb(25, 190, 107);
		font-size: 20px;
	}

	.msg {
		display: flex;
		flex-direction: column;
		font-size: 18px;

	}
</style>
