<template>
	<view :class="['n-flex-row', 'n-align-center', 'n-bg-'+bgType, 'n-border-'+border, 'n-radius-'+radius]" :style="boxStyle">
		<view v-if="indexOfDays !== -1" :class="['n-flex-row', 'n-justify-center', 'n-align-center', 'n-height-'+itemHeight, 'n-bg-'+itemBgType, 'n-border-'+itemBorder, 'n-radius-'+itemRadius]" :style="mrItemBoxStyle">
			<text :class="['n-color-'+itemTextType, 'n-size-'+itemTextSize]" :style="itemTextStyle">{{countDownData.day}}</text>
		</view>
		<view v-if="indexOfDays !== -1" class="n-flex-row n-justify-center n-align-center" :style="mrIndicatorBoxStyle">
			<text :class="['n-color-'+indicatorTextType, 'n-size-'+indicatorTextSize]" :style="indicatorTextStyle">{{getDot(indexOfDays, indexOfHours)}}</text>
		</view>
		<view v-if="indexOfHours !== -1" :class="['n-flex-row', 'n-justify-center', 'n-align-center', 'n-height-'+itemHeight, 'n-bg-'+itemBgType, 'n-border-'+itemBorder, 'n-radius-'+itemRadius]" :style="mrItemBoxStyle">
			<text :class="['n-color-'+itemTextType, 'n-size-'+itemTextSize]" :style="itemTextStyle">{{countDownData.hour}}</text>
		</view>
		<view v-if="indexOfHours !== -1" class="n-flex-row n-justify-center n-align-center" :style="mrIndicatorBoxStyle">
			<text :class="['n-color-'+indicatorTextType, 'n-size-'+indicatorTextSize]" :style="indicatorTextStyle">{{getDot(indexOfHours, indexOfMinutes)}}</text>
		</view>
		<view v-if="indexOfMinutes !== -1" :class="['n-flex-row', 'n-justify-center', 'n-align-center', 'n-height-'+itemHeight, 'n-bg-'+itemBgType, 'n-border-'+itemBorder, 'n-radius-'+itemRadius]" :style="mrItemBoxStyle">
			<text :class="['n-color-'+itemTextType, 'n-size-'+itemTextSize]" :style="itemTextStyle">{{countDownData.minute}}</text>
		</view>
		<view v-if="indexOfMinutes !== -1&&getDot(indexOfMinutes, indexOfSeconds)" class="n-flex-row n-justify-center n-align-center" :style="mrIndicatorBoxStyle">
			<text :class="['n-color-'+indicatorTextType, 'n-size-'+indicatorTextSize]" :style="indicatorTextStyle">{{getDot(indexOfMinutes, indexOfSeconds)}}</text>
		</view>
		<view v-if="indexOfSeconds !== -1" :class="['n-flex-row', 'n-justify-center', 'n-align-center', 'n-height-'+itemHeight, 'n-bg-'+itemBgType, 'n-border-'+itemBorder, 'n-radius-'+itemRadius]" :style="mrItemBoxStyle">
			<text :class="['n-color-'+itemTextType, 'n-size-'+itemTextSize]" :style="itemTextStyle">{{countDownData.second}}</text>
		</view>
		<view v-if="indexOfSeconds !== -1&&getDot(indexOfSeconds, -1)" class="n-flex-row n-justify-center n-align-center" :style="mrIndicatorBoxStyle">
			<text :class="['n-color-'+indicatorTextType, 'n-size-'+indicatorTextSize]" :style="indicatorTextStyle">{{getDot(indexOfSeconds, -1)}}</text>
		</view>
	</view>
</template>

<script>
	/**
	 * countdown
	 * @description ???????????????????????????
	 * @property {Number} time ???????????????
	 * @property {Number} interval ????????????
	 * @property {String} tpl ??????????????????
	 * @property {String} bgType ????????????
	 * @property {String} border ????????????
	 * @property {String} radius ????????????
	 * @property {String} itemBgType item???????????????
	 * @property {String} itemBorder item???????????????
	 * @property {String} itemRadius item???????????????
	 * @property {String} itemTextType item?????????????????????
	 * @property {String} itemTextSize item?????????????????????
	 * @property {String} itemHeight item???????????????
	 * @property {String} itemWidth item????????????0??????????????????24rpx???padding
	 * @property {String} indicatorTextType ????????????????????????
	 * @property {String} indicatorTextSize ????????????????????????
	 * @property {String} indicatorWidth ??????????????????
	 * @property {String} boxStyle ????????????
	 * @property {String} itemBoxStyle item????????????
	 * @property {String} indicatorBoxStyle ????????????????????????
	 * @property {String} itemTextStyle item????????????
	 * @property {String} indicatorTextStyle ????????????????????????
	*/
	export default {
		props: {
			/**
			 * ???????????????
			 */
			time: {
				type: Number,
				default: 1600000000000
			},
			/**
			 * ????????????
			 */
			interval: {
				type: Number,
				default: 1000
			},
			/**
			 * ??????????????????:{d}??? {h}:{m}:{s}
			 */
			tpl: {
				type: String,
				default: '{h}:{m}:{s}'
			},
			/**
			 * ????????????
			 */
			bgType: {
				type: String,
				default: 'none'
			},
			/**
			 * ????????????
			 */
			border: {
				type: String,
				default: ''
			},
			/**
			 * ????????????
			 */
			radius: {
				type: String,
				default: ''
			},
			/**
			 * item???????????????
			 */
			itemBgType: {
				type: String,
				default: ''
			},
			/**
			 * item???????????????
			 */
			itemBorder: {
				type: String,
				default: ''
			},
			/**
			 * item???????????????
			 */
			itemRadius: {
				type: String,
				default: 's'
			},
			/**
			 * item?????????????????????
			 */
			itemTextType: {
				type: String,
				default: 'text'
			},
			/**
			 * item?????????????????????
			 */
			itemTextSize: {
				type: String,
				default: 'l'
			},
			/**
			 * item???????????????
			 */
			itemHeight: {
				type: String,
				default: 'base'
			},
			/**
			 * item????????????0??????????????????24rpx???padding
			 */
			itemWidth: {
				type: String,
				default: '0'
			},
			/**
			 * ????????????????????????
			 */
			indicatorTextType: {
				type: String,
				default: 'text'
			},
			/**
			 * ????????????????????????
			 */
			indicatorTextSize: {
				type: String,
				default: 'l'
			},
			/**
			 * ??????????????????
			 */
			indicatorWidth: {
				type: String,
				default: '60rpx'
			},
			/**
			 * ????????????
			 */
			boxStyle: {
				type: String,
				default: ''
			},
			/**
			 * item????????????
			 */
			itemBoxStyle: {
				type: String,
				default: ''
			},
			/**
			 * ????????????????????????
			 */
			indicatorBoxStyle: {
				type: String,
				default: ''
			},
			/**
			 * item???????????????
			 */
			itemTextStyle: {
				type: String,
				default: ''
			},
			/**
			 * ????????????????????????
			 */
			indicatorTextStyle: {
				type: String,
				default: ''
			}
		},
		data: () => ({
			NOW_DATE: Date.now(),
			completed: false,
			indexOfDays: -1,
			indexOfHours: -1,
			indexOfMinutes: -1,
			indexOfSeconds: -1
		}),
		mounted() {
			setInterval(() => {
				this.NOW_DATE = Date.now();
			}, this.interval);
			this.indexOfDays = this.tpl.indexOf('d');
			this.indexOfHours = this.tpl.indexOf('h');
			this.indexOfMinutes = this.tpl.indexOf('m');
			this.indexOfSeconds = this.tpl.indexOf('s');
		},
		computed: {
			countDownData() {
				const timeSpacing = this.time - this.NOW_DATE;
				if (timeSpacing < 0) {
					if (this.completed === false) {
						this.$emit('completed');
					}
					this.setCompleted()
					return {
						day: '00',
						hour: '00',
						minute: '00',
						second: '00'
					}
				}
				let day = 0;
				let hour = 0;
				let minute = 0;
				let second = 0;
				if (this.indexOfDays !== -1) {
					day = Math.floor(timeSpacing / (24 * 60 * 60 * 1000));
					hour = Math.floor(timeSpacing % (24 * 60 * 60 * 1000) / (60 * 60 * 1000));
				} else {
					day = 0;
					hour = Math.floor(timeSpacing / (60 * 60 * 1000));
				}
				if (this.indexOfHours !== -1) {
					hour = Math.floor((timeSpacing - day * 24 * 60 * 60 * 1000) / (60 * 60 * 1000));
					minute = Math.floor((timeSpacing - day * 24 * 60 * 60 * 1000) % (60 * 60 * 1000) / (60 * 1000));
				} else {
					hour = 0;
					minute = Math.floor((timeSpacing - day * 24 * 60 * 60 * 1000) / (60 * 1000));
				}
				if (this.indexOfMinutes !== -1) {
					minute = Math.floor((timeSpacing - day * 24 * 60 * 60 * 1000 - hour * 60 * 60 * 1000) / (60 * 1000));
					second = Math.floor((timeSpacing - day * 24 * 60 * 60 * 1000 - hour * 60 * 60 * 1000) % (60 * 1000) / 1000);
				} else {
					minute = 0;
					second = Math.floor((timeSpacing - day * 24 * 60 * 60 * 1000 - hour * 60 * 60 * 1000) / 1000);
				}
				return {
					day: day < 10 ? '0' + day : '' + day,
					hour: hour < 10 ? '0' + hour : '' + hour,
					minute: minute < 10 ? '0' + minute : '' + minute,
					second: second < 10 ? '0' + second : '' + second
				}
			},
			mrItemBoxStyle() {
				const _width = parseInt(this.itemWidth)
				let _style = ''
				if (_width <= 0) {
					_style += `padding-left:24rpx;padding-right:24rpx;`
				} else {
					_style += `width:${this.itemWidth};`
				}
				return _style + this.itemBoxStyle
			},
			mrIndicatorBoxStyle() {
				let _style = ''
				if (this.indicatorWidth && this.indicatorWidth.length > 0) {
					_style = `width:${this.indicatorWidth};`
				}
				return _style + this.indicatorBoxStyle
			}
		},
		methods: {
			getDot(prevTagIndex, nextTagIndex = -1) {
				if (nextTagIndex === -1) {
					return this.tpl.slice(prevTagIndex + 2)
				}
				return this.tpl.slice(prevTagIndex + 2, nextTagIndex - 1)
			},
			setCompleted() {
				this.completed = true;
			}
		}
	}
</script>

<style lang="scss" scoped>
</style>
