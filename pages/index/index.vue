<template>
	<view class="container">
		<view class="header">
			<view class="header-character"><text>推荐</text>
			</view>
			<view class="header-character"><text>专题</text>
			</view>
			<view class="header-character"><text>连载</text>
			</view>
		</view>

		<h3>这里还什么都没有哦~</h3>
		<h5>点击右下角按钮写第一篇文章~</h5>
		<navigator url="../write/write" hover-class="navigator-hover">
			<button class="circle-btn"><text class="icon-text">+</text></button>
		</navigator>
	</view>
</template>

<script>
	export default {
		data() {
			return {

			};
		},
		onLoad() {},
		methods: {

		}
	};
</script>

<style>
	.icon-text {
		font-size: 20pt;
		color: #fff;
	}

	.circle-btn {
		position: absolute;
		bottom: 20px;
		right: 10px;
		width: 45px;
		height: 45px;
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

	.header {
		width: 60%;
		display: flex;
	}

	.header-character {
		height: 30px;
		width: 33.3%;
		color:#DE533A;
		/* border: 1px solid black; */
	}
</style>
