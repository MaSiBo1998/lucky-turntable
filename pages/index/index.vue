<template>
	<page-meta :root-font-size="rootFontSize + 'px'">
	</page-meta>
	<view class="content">
		转盘展示:
		<!-- 
		 prizeList: 奖品列表(title:奖品名称,img:奖品样式图片)
		 rotationTime: 旋转时间(毫秒)
		 prizeIndex: 获得第几个奖品(注意不是数组下标)
		 times: 多少次抽奖机会
		 -->
		<LuckyTurntable :prizeList = "prizeList" :rotationTime = "rotationTime" :prizeIndex="prizeIndex" :times.sync="times" @timesChange= "timesChange"></LuckyTurntable>
	</view>

</template>

<script>
	import LuckyTurntable from '../../components/LuckyTurntable/index.vue'
	export default {
		components: {
			LuckyTurntable
		},
		data() {
			return {
				rootFontSize: 14,
				panziElement: null,
				rotationTime: 5000, // 旋转时间
				times: 3, //抽奖剩余次数
				prizeIndex: 2,//获得第几个奖品
				prizeList: [{
						title: '特等奖'
					},
					{
						title: '一等奖'
					},
					{
						title: '二等奖'
					},
					{
						title: '三等奖'
					},
					{
						title: '谢谢参与'
					},
					{
						title: '5元现金'
					},
					{
						title: '20元现金'
					},
					{
						title: '50元现金'
					}
				],

			}
		},
		onLoad() {
			let srceenNunber = 375;
			let that = this;
			//窗体改变大小触发事件
			uni.onWindowResize((res) => {
				that.rootFontSize = parseFloat(res.size.windowWidth / srceenNunber) * 14
			})
			//打开获取屏幕大小
			uni.getSystemInfo({
				success(res) {
					that.os = res.osName
					that.rootFontSize = parseFloat(res.screenWidth / srceenNunber) * 14;
				}
			})
		},
		methods: {
			timesChange() {
				this.times--
			}
		},
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

				/* 盘子样式 */
				.panzi {
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
					.jiang {
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
						box-shadow: 0 0 5px red;
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
