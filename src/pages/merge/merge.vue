<template>
	<view class="content"
		@touchstart="start" 
		@touchend="end" 
		@touchmove="move">
		<view class="operate">
			<button @click="startMove">移动</button>
			<button @click="startZoom">缩放</button>
			<button @click="startSave">保存</button>
		</view>
		<canvas ref="healthyCode" canvas-id="healthyCode" id="healthyCode"></canvas>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				ctx: null,
				scenes: null,
				nucleiAcid: null,
				x: 0,
				y: 0,
				srcZoom: 0,
				zoom: 0,
				clientX: 0,
				clientY: 0,
				operate: 1,
			}
		},
		onLoad() {
			this.scenes = JSON.parse(uni.getStorageSync("scenes"));
			this.nucleiAcid = JSON.parse(uni.getStorageSync("nucleiAcid"));
			if (!this.scenes || !this.nucleiAcid) {
				uni.showToast({
					icon: "error",
					title: "获取失败"
				})
			}
		},
		onReady() {
			if (this.scenes && this.nucleiAcid) {
				var info = uni.getSystemInfoSync();
				this.srcZoom = this.scenes.w / info.windowWidth;
				this.zoom = this.srcZoom;
				
				this.ctx = uni.createCanvasContext('healthyCode', this)
				this.ctx.drawImage(this.scenes.p, 0, 0, this.scenes.w / this.srcZoom, this.scenes.h / this.srcZoom)
				this.ctx.drawImage(this.nucleiAcid.p, this.x, this.y, this.nucleiAcid.w / this.zoom, this.nucleiAcid.h / this.zoom)
				this.ctx.draw();
			}
		},
		methods: {
			start(e) {
				this.clientX = e.changedTouches[0].clientX;
				this.clientY = e.changedTouches[0].clientY;
			},
			end(e) {
				if (this.operate === 1) {
					this.x += e.changedTouches[0].clientX - this.clientX;
					this.y += e.changedTouches[0].clientY - this.clientY;
				} else if (this.operate === 2) {
					this.zoom -= (e.changedTouches[0].clientY - this.clientY) / 100;
				}
			},
			move(event) {
				var touch = event.touches[0];
				if (this.operate === 1) {
					var x = this.x + touch.clientX - this.clientX;
					var y = this.y + touch.clientY - this.clientY;
					if (this.ctx) {
						this.ctx.drawImage(this.scenes.p, 0, 0, this.scenes.w / this.srcZoom, this.scenes.h / this.srcZoom)
						this.ctx.drawImage(this.nucleiAcid.p, x, y, this.nucleiAcid.w / this.zoom, this.nucleiAcid.h / this.zoom)
						this.ctx.draw();
					}
				} else if (this.operate === 2) {
					var zoom = this.zoom - (touch.clientY - this.clientY) / 100;
					if (this.ctx) {
						this.ctx.drawImage(this.scenes.p, 0, 0, this.scenes.w / this.srcZoom, this.scenes.h / this.srcZoom)
						this.ctx.drawImage(this.nucleiAcid.p, this.x, this.y, this.nucleiAcid.w / zoom, this.nucleiAcid.h / zoom)
						this.ctx.draw();
					}
				}
			},
			startMove() {
				this.operate = 1;
			},
			startZoom() {
				this.operate = 2;
			},
			startSave() {
				uni.canvasToTempFilePath({
				  canvasId: 'healthyCode',
				  success: function(res) {
				    console.log(res)
					uni.saveImageToPhotosAlbum({
						filePath: res.tempFilePath,
						success: function() {
							uni.showToast({
								icon: "success",
								title: "保存成功"
							})
						},
						fail: function() {
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
					  	title: "生成失败"
					  })
				  }
				})
			},
		}
	}
</script>

<style>
	page {
		height: 100%;
	}
	.content {
		height: 100%;
	}
	canvas {
		width: 100%;
		height: 100%;
	}
	
	.operate {
		position: fixed;
		right: 0;
		z-index: 999;
	}
</style>
