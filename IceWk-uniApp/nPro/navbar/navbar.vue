<template>
	<view :style="boxStyle">
		<view v-if="fixed&&isSeize" :style="{height: seizeHeight + 'px', width: '750rpx'}"></view>
		<view :class="['n-bg-'+bgType, 'n-border-'+border, 'n-nav-box', fixed&&'n-nav-fixed']" :style="statusNavStyle" @tap.stop="toPrevent">
			<view v-if="includeStatus" :style="{width: '750rpx', height: statusHeight + 'px'}"></view>
			<view class="n-flex-row n-wrap-nowrap n-align-center" :style="mrNavStyle">
				<view :class="['n-flex-row', 'n-wrap-nowrap', 'n-justify-start', 'n-align-center', 'n-nav-lefts', leftBgType&&('n-bg-'+leftBgType)]" :style="leftStyle">
					<slot name="left">
						<view v-if="lefts && lefts.length > 0" v-for="(left, idx) in lefts" :key="idx" :class="['n-position-relative', 'n-flex-row', 'n-align-center', left.bgType&&('n-bg-'+left.bgType)]" :style="leftItemStyle+(left.style||'')" bubble="true" @tap="goLeft(idx)">
							<n-icon v-if="left.icon" :name="left.icon" :type="left.iconType||itemIconType" :size="left.iconSize||itemIconSize" :iconStyle="itemIconStyle+(left.iconStyle||'')" @iconClicked="goLeft(idx)"></n-icon>
							<text v-if="left.text" :class="['n-color-'+(left.textType||itemTextType), 'n-size-'+(left.textSize||itemTextSize)]" :style="itemTextStyle+(left.textStyle||'')">{{left.text}}</text>
							<n-badge v-if="left.badge" :size="left.badge.size||badgeConfig.size||'12rpx'" :bgType="left.badge.bgType||badgeConfig.bgType||'error'" :text="left.badge.text||''" :border="left.badge.border||badgeConfig.border||'none'" :textType="left.badge.textType||badgeConfig.textType||'inverse'" :textSize="left.badge.textSize||badgeConfig.textSize||'ss'" :textStyle="left.badge.textStyle||badgeConfig.textStyle||''" :boxStyle="left.badge.boxStyle||badgeConfig.boxStyle||''"></n-badge>
						</view>
					</slot>
				</view>
				<view class="n-flex-row n-wrap-nowrap n-align-center n-justify-center n-nav-title" :style="centerStyle">
					<slot name="center">
						<text v-if="title" :class="['n-lines-1', 'n-nav-title-text', 'n-color-'+titleType, 'n-size-'+titleSize]" :style="titleStyle" @tap.stop="clickCenter">{{ title }}</text>
						<n-icon v-if="icon" :name="icon" :type="iconType" :size="iconSize" :iconStyle="iconStyle" @iconClicked="clickCenter"></n-icon>
					</slot>
				</view>
				<view :class="['n-flex-row', 'n-align-center', 'n-wrap-nowrap', 'n-justify-end', 'n-nav-rights', rightBgType&&('n-bg-'+rightBgType)]" :style="rightStyle">
					<slot name="right">
						<view v-if="rights && rights.length > 0" v-for="(right, idx) in rights" :key="idx" :class="['n-position-relative', 'n-flex-row', 'n-align-center', 'n-wrap-nowrap', 'n-justify-end', right.bgType&&('n-bg-'+right.bgType)]" :style="rightItemStyle+(right.style||'')" bubble="true" @tap="goRight(idx)">
							<n-icon v-if="right.icon" :name="right.icon" :type="right.iconType||itemIconType" :size="right.iconSize||itemIconSize" :iconStyle="itemIconStyle+(right.iconStyle||'')" @iconClicked="goRight(idx)"></n-icon>
							<text v-if="right.text" :class="['n-color-'+(right.textType||itemTextType), 'n-size-'+(right.textSize||itemTextSize)]" :style="itemTextStyle+(right.textStyle||'')">{{right.text}}</text>
							<n-badge v-if="right.badge" :size="right.badge.size||badgeConfig.size||'12rpx'" :bgType="right.badge.bgType||badgeConfig.bgType||'error'" :text="right.badge.text||''" :border="right.badge.border||badgeConfig.border||'none'" :textType="right.badge.textType||badgeConfig.textType||'inverse'" :textSize="right.badge.textSize||badgeConfig.textSize||'ss'" :textStyle="right.badge.textStyle||badgeConfig.textStyle||''" :boxStyle="right.badge.boxStyle||badgeConfig.boxStyle||''"></n-badge>
						</view>
					</slot>
				</view>
			</view>
			<slot name="extra"></slot>
		</view>
	</view>
</template>

<script>
	import {getStatusBarHeight} from '../utils/system.js'
	
	/**
	 * navbar
	 * @description ??????????????????
	 * @property {Array}  lefts ??????item???
	 * @property {String} title ??????
	 * @property {String} icon ??????????????????
	 * @property {Array} rights ??????item???
	 * @property {String} bgType ????????????
	 * @property {String} border ????????????
	 * @property {String} titleType ??????????????????
	 * @property {String} titleSize ??????????????????
	 * @property {String} titleStyle ????????????
	 * @property {String} iconType ????????????????????????
	 * @property {String} iconSize ????????????????????????
	 * @property {String} iconStyle ??????????????????
	 * @property {String} itemIconType item??????????????????
	 * @property {String} itemIconSize item??????????????????
	 * @property {String} itemIconStyle item????????????
	 * @property {String} itemTextType item??????????????????
	 * @property {String} itemTextSize item??????????????????
	 * @property {String} itemTextStyle item????????????
	 * @property {Number} height ?????????px??????
	 * @property {Boolean} includeStatus ?????????????????????
	 * @property {Boolean} isSeize ????????????
	 * @property {Boolean} fixed ??????fixed??????
	 * @property {String} leftStyle lefts????????????
	 * @property {String} leftBgType lefts?????????????????????
	 * @property {String} leftItemStyle lefts???item?????????
	 * @property {String} rightStyle rights????????????
	 * @property {String} rightBgType rights??????????????????
	 * @property {String} rightItemStyle rights???item?????????
	 * @property {String} centerStyle ??????title???????????????
	 * @property {String} boxStyle ????????????
	 * @property {String} statusNavStyle status???nav???????????????
	 * @property {String} navStyle nav???????????????
	 * @property {Object} badgeConfig badge????????????
	 */
	export default {
		props: {
			/**
			 * ??????item???
			 */
			lefts: {
				type: Array,
				default: ()=>{return []}
			},
			/**
			 * ??????
			 */
			title: {
				type: String,
				default: null
			},
			/**
			 * ??????????????????
			 */
			icon: {
				type: String,
				default: null
			},
			/**
			 * ??????item???
			 */
			rights: {
				type: Array,
				default: ()=>{return []}
			},
			/**
			 * ????????????
			 */
			bgType: {
				type: String,
				default: 'nav'
			},
			/**
			 * ????????????
			 */
			border: {
				type: String,
				default: ''
			},
			/**
			 * ??????????????????
			 */
			titleType: {
				type: String,
				default: 'nav-title'
			},
			/**
			 * ??????????????????
			 */
			titleSize: {
				type: String,
				default: 'nav-title'
			},
			/**
			 * ????????????
			 */
			titleStyle: {
				type: String,
				default: ''
			},
			/**
			 * ????????????????????????
			 */
			iconType: {
				type: String,
				default: 'nav-title'
			},
			/**
			 * ????????????????????????
			 */
			iconSize: {
				type: String,
				default: 'nav-title'
			},
			/**
			 * ??????????????????
			 */
			iconStyle: {
				type: String,
				default: ""
			},
			/**
			 * item??????????????????
			 */
			itemIconType: {
				type: String,
				default: 'nav-icon'
			},
			/**
			 * item??????????????????
			 */
			itemIconSize: {
				type: String,
				default: 'nav-icon'
			},
			/**
			 * item????????????
			 */
			itemIconStyle: {
				type: String,
				default: ""
			},
			/**
			 * item??????????????????
			 */
			itemTextType: {
				type: String,
				default: 'nav-item'
			},
			/**
			 * item??????????????????
			 */
			itemTextSize: {
				type: String,
				default: 'nav-item'
			},
			/**
			 * item????????????
			 */
			itemTextStyle: {
				type: String,
				default: ''
			},
			/**
			 * ?????????px??????
			 */
			height: {
				type: Number,
				default: 44
			},
			/**
			 * ?????????????????????
			 */
			includeStatus: {
				type: Boolean,
				default: true
			},
			/**
			 * ????????????
			 */
			isSeize: {
				type: Boolean,
				default: true
			},
			/**
			 * ??????fixed??????
			 */
			fixed: {
				type: Boolean,
				default: true
			},
			/**
			 * lefts????????????
			 */
			leftStyle: {
				type: String,
				default: ''
			},
			/**
			 * lefts?????????????????????
			 */
			leftBgType: {
				type: String,
				default: ''
			},
			/**
			 * lefts???item?????????
			 */
			leftItemStyle: {
				type: String,
				default: ''
			},
			/**
			 * rights????????????
			 */
			rightStyle: {
				type: String,
				default: ''
			},
			/**
			 * rights??????????????????
			 */
			rightBgType: {
				type: String,
				default: ''
			},
			/**
			 * rights???item?????????
			 */
			rightItemStyle: {
				type: String,
				default: ''
			},
			/**
			 * ??????title???????????????
			 */
			centerStyle: {
				type: String,
				default: ''
			},
			/**
			 * ????????????
			 */
			boxStyle: {
				type: String,
				default: ''
			},
			/**
			 * status???nav???????????????
			 */
			statusNavStyle: {
				type: String,
				default: ''
			},
			/**
			 * nav???????????????
			 */
			navStyle: {
				type: String,
				default: ''
			},
			/**
			 * badge????????????
			 */
			badgeConfig: {
				type: Object,
				default: ()=>{
					return {}
				}
			}
		},
		data() {
			return {
			}
		},
		computed: {
			statusHeight() {
				if (this.includeStatus) {
					return getStatusBarHeight()
				}
				return 0
			},
			seizeHeight() {
				return this.statusHeight + this.height
			},
			mrNavStyle() {
				let _style = "height:" + this.height + 'px;'
				_style += this.navStyle
				return _style
			}
		},
		methods: {
			goLeft(i) {
				this.$emit("leftAction", i)
			},
			clickCenter(e) {
				e && e.stopPropagation && e.stopPropagation()
				this.$emit("centerAction")
			},
			goRight(i) {
				this.$emit("rightAction", i)
			},
			toPrevent(e) {
				e && e.stopPropagation && e.stopPropagation()
			}
		}
	}
</script>

<style lang="scss" scoped>
	.n-nav {
		&-box {
			width: 750rpx;
			left: 0;
			top: 0;
		}
		&-fixed {
			position: fixed;
		}
		&-lefts {
			padding-left: 30rpx;
			width: 200rpx;
		}
		&-title {
			width: 350rpx;
			
			&-text {
				// max-width: 350rpx;
			}
		}
		&-rights {
			padding-right: 30rpx;
			width: 200rpx;
		}
	}
</style>
