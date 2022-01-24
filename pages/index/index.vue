<template>
	<view class="index">
		<button class="button" style="margin-top: 200rpx;" type="primary" @click="onClick(0)">静态海报</button>
		<button class="button" type="primary" @click="onClick(1)">海报编辑器</button>
	</view>
</template>

<script>
	export default {
		methods: {
			onClick(type) {
				let url = ''
				if (!type) {
					url = '/pages/static-painter/index'
				} else {
					url = '/pages/dance-painter/index'
				}
				uni.navigateTo({
					url
				})
			}
		},
	};
</script>

<style>
	.button {
		position: relative;
		width: 60vw;
		margin-top: 80rpx;
	}
</style>
