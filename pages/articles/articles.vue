<template>
	<view>
		<view class="bellow" >
			<uni-list>
				<uni-list-item :title="article.title" v-for="(article, index) in articles" :key="index" thumb="https://upload-images.jianshu.io/upload_images/16150151-477ce947c1d1f48c.jpeg?imageMogr2/auto-orient/strip|imageView2/1/w/360/h/240"
				 :note="article.content"></uni-list-item>
			</uni-list>
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
				articles: [{
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

				]
			};
		  }
		}
</script>

<style>
</style>
