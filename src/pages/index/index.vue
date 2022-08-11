<template>
	<view class="content">
		<view>
			<button @click="getScenes">添加场景截图</button>
		</view>
		<view class="z"></view>
		<view>
			<button @click="getNucleiAcid">添加核酸截图</button>
		</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				hasScenes: false,
				hasNucleiAcid: false,
			}
		},
		methods: {
			getScenes() {
				var _this = this;
				uni.chooseImage({
					count: 1,
					sizeType: ["original"],
					sourceType: ["album"],
					success: function(res) {
						_this.hasScenes = true;
						
						if (res && res.tempFilePaths && res.tempFilePaths.length === 1 && res.tempFilePaths[0]) {
							uni.getImageInfo({
								src: res.tempFilePaths[0],
								success: function(imageInfo) {
									uni.setStorage({
										key: "scenes",
										data: JSON.stringify({p: res.tempFilePaths[0], w: imageInfo.width, h: imageInfo.height}),
										success: function() {
											uni.showToast({
												icon: "success",
												title: "成功"
											})
											_this.selectDone();
										},
										fail: function() {
											_this.hasScenes = false;
											console.log("saveScenes error", err);
											uni.showToast({
												icon: "error",
												title: "保存失败"
											})
										}
									})
								},
								fail: function() {
									uni.showToast({
										icon: "error",
										title: "读取信息失败"
									})
								}
							})
						}
					},
					fail: function(err) {
						_this.hasScenes = false;
						console.log("getScenes error", err);
						uni.showToast({
							icon: "error",
							title: "失败"
						})
					}
				})
			},
			getNucleiAcid() {
				var _this = this;
				uni.chooseImage({
					count: 1,
					sizeType: ["original"],
					sourceType: ["album"],
					success: function(res) {
						_this.hasNucleiAcid = true;
						if (res && res.tempFilePaths && res.tempFilePaths.length === 1 && res.tempFilePaths[0]) {
							uni.getImageInfo({
								src: res.tempFilePaths[0],
								success: function(imageInfo) {
									uni.setStorage({
										key: "nucleiAcid",
										data: JSON.stringify({p: res.tempFilePaths[0], w: imageInfo.width, h: imageInfo.height}),
										success: function() {
											uni.showToast({
												icon: "success",
												title: "成功"
											})
											_this.selectDone();
										},
										fail: function() {
											_this.hasScenes = false;
											console.log("saveNucleiAcid error", err);
											uni.showToast({
												icon: "error",
												title: "保存失败"
											})
										}
									})
								},
								fail: function() {
									uni.showToast({
										icon: "error",
										title: "读取信息失败"
									})
								}
							})
						}
					},
					fail: function(err) {
						_this.hasNucleiAcid = false;
						console.log("getNucleiAcid error", err);
						uni.showToast({
							icon: "error",
							title: "失败"
						})
					}
				})
			},
			selectDone() {
				if (this.hasScenes && this.hasNucleiAcid) {
					this.hasScenes = false;
					this.hasNucleiAcid = false;
					uni.navigateTo({
						url: "../merge/merge"
					})
				}
			},
		}
	}
</script>

<style>
	page {
		height: 100%;
	}
	.content {
		display: flex;
		flex-direction: column;
		justify-content: center;
		height: 100%
	}
	.z {
		height: 3%
	}
</style>
