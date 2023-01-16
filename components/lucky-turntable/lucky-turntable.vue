<template>
	<page-meta :root-font-size="rootFontSize + 'px'">
	</page-meta>
	<view class="content">
		<view class="turntable-box">
			<view class="content-box">
				<view class="zhuanpan"
					:style="{transition:'transform ' + rotationTime / 1000 + 's ease',transform:'rotate(' + rotate + 'deg)'}">
					<view class="bck-box" :style="` transform: rotate(${-90+180/prizeList.length}deg)`">
						<view class="bck" v-for="(i,index) in prizeList" :key="index"
							:style="`transform: rotate(${-index*360/prizeList.length}deg) skew(${-90 + 360/prizeList.length}deg);`">
						</view>
					</view>
					<view class="prize"
						:style="`transform: rotate(${-index*360/prizeList.length}deg) translateY(-7.5rem);`"
						v-for="(i,index) in prizeList" :key="index">
						<span class="title">{{i.title}}</span>
						<view class="img">
							img{{index}}
							<span class="title">{{i.title}}</span>
						</view>
					</view>
				</view>
				<view class="pointer-box" @click="onStart">
					<img src="../../static/pointer.png" alt="">
					<view class="hand" v-if="isHand"></view>
				</view>
			</view>
			<!-- 剩余次数 -->
			<view class="remainder-times">
				剩余次数: {{times}}
			</view>
		</view>
	</view>

</template>

<script>
	export default {
		name: 'LuckyTurntable',
		data() {
			return {
				rootFontSize: 14,
				rotate: 0, // 旋转角度,通过控制旋转角度来指定指向哪个奖品
				rotNum: 1, // 旋转圈数基数
				isHand: true, //手指是否显示
				isManyClick: false, //未出结果频繁点击提示
			}
		},
		props: {
			// 数据列表
			prizeList: {
				type: Array,
				default: [],
			},
			// 转圈时间 毫秒
			rotationTime: {
				type: Number,
				default: 3000,
			},
			// 第几个奖项获奖 毫秒
			prizeIndex: {
				type: Number,
				default: 1,
			},
			// 抽奖剩余次数
			times: {
				type: Number,
				default: 1,
			},
		},
		onLoad() {
			this.lastTimes = this.times
			let srceenNunber = 375;
			let that = this;
			//窗体改变大小触发事件
			uni.onWindowResize((res) => {
				that.rootFontSize = parseFloat(res.size.windowWidth / srceenNunber) * 14
			})
			//打开获取屏幕大小
			uni.getSystemInfo({
				success(res) {
					that.rootFontSize = parseFloat(res.screenWidth / srceenNunber) * 14;
				}
			})
		},
		methods: {
			// 点击开始转动
			onStart() {

				if (this.times <= 0) {
					alert('没有抽奖次数了,请下次再来')
					return false
				}
				if (this.isManyClick) {
					alert('结果还没出现,请稍后')
				} else {

					this.isHand = false
					this.startToRotate(this.prizeIndex);
				}
			},
			// 开始转动
			startToRotate(val) {
				const self = this;
				// 拿到相应的角度调旋转接口
				self.getPrize(val, () => {
					self.completePrize();
				});
			},
			//旋转到第几个奖品 index  complete回调成功函数
			getPrize(index, complete) {

				// 相应的角度 + 满圈 只是在原角度多转了几圈 360 * 6
				// angele  每个商品的扇形角度
				let angle = 360 / this.prizeList.length
				// 旋转的角度叠加
				// index -1  (第一个商品旋转角度为0,所以-1)
				// 1800,旋转的基础角度,必须是360的倍数才能准确
				this.rotate = 1800 * this.rotNum + (index - 1) * angle;
				clearTimeout(this.timer);
				// this.rotate += 120
				// 如果计时器还在,说明转盘还未停止转动,打开避免多次点击提示
				if (this.timer) {
					this.isManyClick = true
				}
				// 设置5秒后停止旋转,处理接口返回的数据
				this.timer = setTimeout(() => {
					complete();
					clearTimeout(this.timer);
					this.timer = null;
					// 结束后可以再次点击
					this.isManyClick = false

					this.rotNum++;
				}, this.rotationTime);
			},
			//得奖后的处理
			completePrize() {
				this.isHand = true
				this.$emit('timesChange');
				alert('恭喜您获得' + this.prizeList[this.prizeIndex - 1].title)
			},
		}
	}
</script>

<style scoped lang="scss">
	// 手指变化
	@keyframes likes {
		0% {
			transform: scale(1);
		}

		25% {
			transform: scale(0.9);
		}

		50% {
			transform: scale(0.85);
		}

		75% {
			transform: scale(0.9);
		}

		100% {
			transform: scale(1);
		}
	}

	.content {
		font-family: SF Pro Text;
		box-sizing: border-box;
		height: 40.5rem;
		position: relative;
		box-sizing: border-box;

		// 转盘
		.turntable-box {
			width: 25.5rem;
			height: 31.643rem;
			margin: 0 auto;
			background: url('../../static/box-content.png') center center;
			background-size: 100% 100%;
			padding-top: 1.357rem;
			box-sizing: border-box;

			// 转盘主体
			.content-box {
				user-select: none;
				display: flex;
				justify-content: center;
				align-items: center;
				position: relative;
				width: 22.071rem;
				height: 22.071rem;
				margin: 0 auto;

				// 指针盒子
				.pointer-box {
					position: absolute;

					img {
						width: 7.929rem;
						height: 8.929rem;
					}

					.hand {
						top: 3.857rem;
						left: 4.5rem;
						position: absolute;
						width: 6.643rem;
						height: 7.429rem;
						background: url('../../static/hand.png') no-repeat;
						background-size: 100% 100%;
						animation-name: likes; // 动画名称
						animation-direction: alternate; // 动画在奇数次（1、3、5...）正向播放，在偶数次（2、4、6...）反向播放。
						animation-timing-function: linear; // 动画执行方式，linear：匀速；ease：先慢再快后慢；ease-in：由慢速开始；ease-out：由慢速结束；ease-in-out：由慢速开始和结束；
						animation-delay: 0s; // 动画延迟时间
						animation-iteration-count: infinite; //  动画播放次数，infinite：一直播放
						animation-duration: 1s; // 动画完成时间
					}
				}

				/* 转盘样式 */
				.zhuanpan {
					overflow: hidden;
					border-radius: 50%;
					position: absolute;
					width: 100%;
					height: 100%;
					left: 0;
					top: 0;
					display: flex;
					justify-content: center;
					align-items: center;
					box-sizing: border-box;

					/* 每个奖项的样式 */
					.prize {
						position: absolute;

						.title {
							font-weight: bold;
							font-size: 18px;
							color: #3b3b3b;
						}

						.img {
							margin: 0.5rem auto;
							width: 2.5rem;
							height: 2.5rem;
							line-height: 2.5rem;
							overflow: hidden;
							color: white;

							img {
								height: 100%;
							}
						}
					}
				}

				.bck-box {
					overflow: hidden;
					position: absolute;
					width: 100%;
					height: 100%;
					left: 0;
					top: 0;
					// background: blue;
					border-radius: 50%;

					/* 转盘扇形背景 */
					.bck {
						box-sizing: border-box;
						position: absolute;
						width: 100%;
						height: 100%;
						opacity: 1;
						top: -50%;
						left: 50%;
						transform-origin: 0% 100%;
						// transform:skew(30deg);
					}

					/* 转盘背景色 */
					.bck:nth-child(2n) {
						background: radial-gradient(99.68% 99.68% at 49.93% 99.97%, rgba(146, 182, 246, 1) 0%, rgba(214, 225, 252, 1) 30.78%, rgba(167, 197, 246, 1) 61.57%, rgba(207, 223, 247, 1) 100%);
					}

					.bck:nth-child(2n + 1) {
						background: #EEE;

					}
				}
			}

			// 剩余次数
			.remainder-times {
				margin: 5.429rem auto 1.786rem;
				width: 9.714rem;
				height: 1rem;
				line-height: 1rem;
				text-align: center;
				color: rgba(255, 255, 255, 1);
				font-weight: 500;
				font-size: 0.857rem;
			}
		}
	}
</style>
