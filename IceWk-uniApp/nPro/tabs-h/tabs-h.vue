<template>
	<scroll-view ref="scroll" id="scroll" :scroll-x="true" :scroll-left="scrollLeft" :scroll-with-animation="true" :show-scrollbar="false" :class="['n-bg-'+bgType, 'n-border-'+border, 'n-flex-row', 'n-tabs-scroll']" :style="mrScrollStyle">
		<view class="n-flex-column n-position-relative">
			<view v-if="hasIndicator&&!hoverTop" ref="n-underline" :class="[absIndicator&&'n-tab-item-underline', 'n-bg-'+indicatorType, isTap?'n-tab-item-animation':'']" :style="mrIndStyle">
				<slot name="indicator"></slot>
			</view>
			<view :class="['n-flex-row', 'n-justify-'+justify, 'n-wrap-nowrap']" :style="mrTabsStyle">
				<view :style="{width: left}"></view>
				<view v-for="(item, index) in items" :key="index" :ref="'item'+index" :id="'item'+index" class="n-flex-column n-justify-center n-align-center n-position-relative" :style="mrItemStyle + (index===value ? activeItemStyle:'') + (index===items.length-1?'margin-right:0;':'')" @click="changeTab(index)">
					<text :class="['n-color-'+(index===value?activeTextType:textType), 'n-size-'+(index===value?activeTextSize:textSize)]" :style="textStyle + (index===value ? activeTextStyle : '')">{{ textLabel ? item[textLabel] : item }}</text>
					<n-badge v-if="item.badge" :size="item.badge.size||badgeConfig.size||'12rpx'" :bgType="item.badge.bgType||badgeConfig.bgType||'error'" :text="item.badge.text||''" :border="item.badge.border||badgeConfig.border||'none'" :textType="item.badge.textType||badgeConfig.textType||'inverse'" :textSize="item.badge.textSize||badgeConfig.textSize||'ss'" :textStyle="item.badge.textStyle||badgeConfig.textStyle||''" :boxStyle="item.badge.boxStyle||badgeConfig.boxStyle||''"></n-badge>
				</view>
				<view :style="{width: right}"></view>
			</view>
			<view v-if="hasIndicator&&hoverTop" ref="n-underline" :class="[absIndicator&&'n-tab-item-underline', 'n-bg-'+indicatorType, isTap?'n-tab-item-animation':'']" :style="mrIndStyle">
				<slot name="indicator"></slot>
			</view>
		</view>
	</scroll-view>
</template>

<script>
	/**
	 * tabs-h
	 * @description ??????tabs???item/indicator????????????????????????????????????
	 * @property {Array} items tabs??????
	 * @property {Number} value ??????index
	 * @property {Number} last ????????????????????????scroll???????????????current?????????????????????????????????????????????????????????
	 * @property {String} textLabel ?????????????????????
	 * @property {Number} offset ?????????swiper???????????????px. swiperScroll dx
	 * @property {String} swiperWidth swiper???????????????????????????
	 * @property {Boolean} isTap ???????????????tab??????????????????????????????true
	 * @property {String} justify center??????end?????????????????????items??????????????????????????????
	 * @property {String} scrollSize scroll??????????????????
	 * @property {String} textSize ?????????????????????
	 * @property {String} activeTextSize ??????????????????????????????
	 * @property {String} bgType ????????????
	 * @property {String} textType ??????????????????
	 * @property {String} activeTextType ??????????????????????????????
	 * @property {String} height ???????????????
	 * @property {String} width tab???????????????0???????????????????????????
	 * @property {String} left ??????????????????
	 * @property {String} right ??????????????????
	 * @property {String} space ??????tab???????????????
	 * @property {String} border ????????????
	 * @property {Boolean} hasIndicator ????????????????????????
	 * @property {String} indicatorType ????????????????????????
	 * @property {String} indicatorWidth ??????????????????????????????0???????????????????????????????????????
	 * @property {String} indicatorHeight ???????????????????????????
	 * @property {String} indicatorRadius ???????????????????????????
	 * @property {Boolean} absIndicator indicator??????????????????
	 * @property {String} absIndicator indicator??????????????????
	 * @property {String} boxStyle ?????????????????????scroll?????????
	 * @property {String} tabsStyle tabs???????????????
	 * @property {String} textStyle ????????????
	 * @property {String} activeTextStyle ????????????????????????
	 * @property {String} itemStyle tab????????????
	 * @property {String} activeItemStyle ????????????tab??????
	 * @property {String} indicatorStyle ??????????????????
	 * @property {Number} updateTime ?????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????items?????????????????????????????????????????????????????????????????????
	 * @property {Number} delay ????????????????????????????????????mounted??????????????????-1????????????????????????????????????updateTime
	 * @property {Number} updateDelay ????????????????????????????????????items?????????????????????-1????????????????????????????????????updateTime
	 * @property {Boolean} hoverTop indicator??????????????????
	 */
	// #ifdef APP-NVUE
	const dom = uni.requireNativePlugin('dom');
	const animation = uni.requireNativePlugin('animation')
	// #endif
	import {getPx} from '../utils/system.js'
	
	export default {
		props: {
			/**
			 * tabs??????
			 */
			items: {
				type: Array,
				default: function() {
					return []
				}
			},
			/**
			 * ??????index
			 */
			value: {
				type: Number,
				default: 0
			},
			/**
			 * ????????????????????????scroll???????????????current?????????????????????
			 * ????????????????????????????????????
			 */
			last: {
				type: Number,
				default: 0
			},
			/**
			 * ?????????????????????
			 */
			textLabel: {
				type: String,
				default: null
			},
			/**
			 * ?????????swiper???????????????px. swiperScroll dx
			 */
			offset: {
				type: Number,
				default: 0
			},
			/**
			 * swiper???????????????????????????
			 */
			swiperWidth: {
				type: String,
				default: '750rpx'
			},
			/**
			 * ???????????????tab??????????????????????????????true
			 */
			isTap: {
				type: Boolean,
				default: false
			},
			/**
			 * justify?????????center??????end?????????????????????items??????????????????????????????
			 */
			justify: {
				type: String,
				default: 'start'
			},
			/**
			 * scroll??????????????????
			 */
			scrollSize: {
				type: String,
				default: '750rpx'
			},
			/**
			 * ?????????????????????
			 */
			textSize: {
				type: String,
				default: 'base'
			},
			/**
			 * ??????????????????????????????
			 */
			activeTextSize: {
				type: String,
				default: 'base'
			},
			/**
			 * ????????????
			 */
			bgType: {
				type: String,
				default: 'inverse'
			},
			/**
			 * ??????????????????
			 */
			textType: {
				type: String,
				default: ''
			},
			/**
			 * ??????????????????????????????
			 */
			activeTextType: {
				type: String,
				default: ''
			},
			/**
			 * ???????????????
			 */
			height: {
				type: String,
				default: '80rpx'
			},
			/**
			 * tab???????????????0???????????????????????????
			 */
			width: {
				type: String,
				default: '120rpx'
			},
			/**
			 * ??????????????????
			 */
			left: {
				type: String,
				default: '0'
			},
			/**
			 * ??????????????????
			 */
			right: {
				type: String,
				default: '0'
			},
			/**
			 * ??????tab???????????????
			 */
			space: {
				type: String,
				default: '0px'
			},
			/**
			 * ????????????
			 */
			border: {
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
			},
			/**
			 * ????????????????????????
			 */
			hasIndicator: {
				type: Boolean,
				default: true
			},
			/**
			 * ????????????????????????
			 */
			indicatorType: {
				type: String,
				default: 'primary'
			},
			/**
			 * ??????????????????????????????0???????????????????????????????????????
			 */
			indicatorWidth: {
				type: String,
				default: '80rpx'
			},
			/**
			 * ???????????????????????????
			 */
			indicatorHeight: {
				type: String,
				default: '5rpx'
			},
			/**
			 * ???????????????????????????
			 */
			indicatorRadius: {
				type: String,
				default: '4rpx'
			},
			/**
			 * indicator??????????????????
			 */
			absIndicator: {
				type: Boolean,
				default: true
			},
			/**
			 * ?????????????????????scroll?????????
			 */
			boxStyle: {
				type: String,
				default: ''
			},
			/**
			 * tabs???????????????
			 */
			tabsStyle: {
				type: String,
				default: ''
			},
			/**
			 * ????????????
			 */
			textStyle: {
				type: String,
				default: ''
			},
			/**
			 * ????????????????????????
			 */
			activeTextStyle: {
				type: String,
				default: ''
			},
			/**
			 * tab????????????
			 */
			itemStyle: {
				type: String,
				default: ''
			},
			/**
			 * ????????????tab??????
			 */
			activeItemStyle: {
				type: String,
				default: ''
			},
			/**
			 * ??????????????????
			 */
			indicatorStyle: {
				type: String,
				default: ''
			},
			/**
			 * ????????????????????????????????????????????????
			 * ???????????????????????????????????????????????????????????????
			 * ??????????????????items?????????????????????
			 * ????????????????????????????????????????????????
			 */
			updateTime: {
				type: Number,
				default: 0
			},
			/**
			 * ???????????????????????????
			 * ?????????mounted??????????????????
			 * -1??????????????????
			 * ??????????????????updateTime
			 */
			delay: {
				type: Number,
				default: -1
			},
			/**
			 * ???????????????????????????
			 * ?????????items?????????????????????
			 * -1??????????????????
			 * ??????????????????updateTime
			 */
			updateDelay: {
				type: Number,
				default: -1
			},
			/**
			 * indicator??????????????????
			 */
			hoverTop: {
				type: Boolean,
				default: false
			}
		},
		data() {
			return {
				// px.?????????????????????indicator
				dyIndicatorWidth: 0,
				dyIndicatorLeft: 0,
				// ???????????????????????????????????????
				dyItems: {},
				scrollLeft: 0,
				timer: null
			}
		},
		computed: {
			mrIndStyle() {
				let _style = `border-radius:${this.indicatorRadius};`
				_style += `height:${this.indicatorHeight};`
				// #ifndef APP-NVUE
				_style += `width:${this.dyIndicatorWidth}px;`
				_style += `left:${this.dyIndicatorLeft}px;`
				// #endif
				return _style + this.indicatorStyle
			},
			spacePx() {
				return getPx(this.space)
			},
			scrollSizePx() {
				return getPx(this.scrollSize)
			},
			widthPx() {
				return getPx(this.width)
			},
			heightPx() {
				return getPx(this.height)
			},
			fixedInd() {
				const indW = parseInt(this.indicatorWidth)
				if (indW > 0) {
					return true
				}
				const iw = parseInt(this.width)
				if (iw > 0) {
					return true
				}
				return false
			},
			fixedIndWidth() {
				const indW = parseInt(this.indicatorWidth)
				if (indW > 0) {
					return this.indicatorWidth
				}
				const iw = parseInt(this.width)
				if (iw > 0) {
					return this.width
				}
				return '0'
			},
			fixedIndWidthPx() {
				return getPx(this.fixedIndWidth)
			},
			mrScrollStyle() {
				return "width:" + this.scrollSizePx + 'px;' + this.boxStyle
			},
			mrTabsStyle() {
				let _style = ''
				if (this.justify != 'start') {
					_style += "width:" + this.scrollSizePx + 'px;'
				}
				return _style + this.tabsStyle
			},
			mrItemStyle() {
				let _style = `height:${this.heightPx}px;`
				const w = parseInt(this.width)
				if (w > 0) {
					_style += `width:${this.widthPx}px;margin-right:${this.spacePx}px;`
				} else {
					_style += `margin-right:${this.spacePx}px;`
				}
				return _style + this.itemStyle
			},
			swiperWidthPx() {
				return getPx(this.swiperWidth)
			}
		},
		mounted() {
			if (this.delay >= 0) {
				const that = this
				setTimeout(()=>{
					that.toCacheItemsSize()
					that.toCurrentIndex(that.value, true)
				}, this.delay)
			} else {
				this.toCacheItemsSize()
				this.toCurrentIndex(this.value, true)
			}
		},
		watch: {
			value(newV) {
				// #ifdef APP-NVUE
				this.toCurrentIndex(newV)
				// #endif
				// #ifndef APP-NVUE
				// ?????????????????????????????????????????????
				this.timer && clearTimeout(this.timer)
				this.timer = setTimeout(()=>{
					this.toCurrentIndex(newV)
					this.timer = null
				}, 0)
				// #endif
			},
			items() {
				// ?????????
				this.dyItems = {}
				const that = this
				this.$nextTick(()=>{
					if (that.updateDelay >= 0) {
						setTimeout(()=>{
							that.toCacheItemsSize()
							that.toCurrentIndex(this.value, true)
						}, that.updateDelay)
					} else {
						that.toCacheItemsSize()
						that.toCurrentIndex(this.value, true)
					}
				})
			},
			offset(newV) {
				this.toHandleSwiperScroll(newV)
			},
			updateTime() {
				this.dyItems = {}
				this.toCacheItemsSize()
				this.toCurrentIndex(this.value, true)
			}
		},
		methods: {
			changeTab(index) {
				this.$emit('change', index)
			},
			async toCurrentIndex(index, isInit=false) {
				let nowWidth = 0
				let _left = 0
				let indWidth = 0
				let indLeft = 0
				let scrollL = 0
				// first to get scroll position
				const cachedScroll = this.dyItems['scroll']
				if (cachedScroll) {
					scrollL = cachedScroll.left
				} else {
					try{
						const res = await this.getElSize(-100)
						scrollL = res.left
						this.dyItems['scroll'] = {left: scrollL}
					}catch(e){
						//TODO handle the exception
					}
				}
				const cached = this.dyItems['item'+index]
				if (cached) {
					_left = cached.left
					nowWidth = cached.width
					indWidth = cached.ind
					indLeft = cached.indLeft
				} else {
					try{
						const result = await this.getElSize(index)
						_left = result.left - scrollL
						nowWidth = result.width
						if (this.fixedInd) {
							indWidth = this.fixedIndWidthPx
						} else {
							indWidth = nowWidth
						}
						indLeft = _left + (nowWidth - indWidth) * 0.5
						// into cache
						this.dyItems['item'+index] = {width: nowWidth, left: _left, ind: indWidth, indLeft: indLeft}
					}catch(e){
						//TODO handle the exception
					}
				}
				const _width = _left + nowWidth + this.spacePx
				// ??????indicator
				this.dyIndicatorWidth = indWidth
				this.dyIndicatorLeft = indLeft
				// #ifdef APP-NVUE
				const indEl = this.$refs['n-underline']
				if (isInit) {
					// ios????????????????????????????????????????????????duration?????????
					animation.transition(indEl, {
						styles: {
							width: `${this.dyIndicatorWidth}px`,
							transform: `translateX(${this.dyIndicatorLeft}px)`
						},
						duration: 0
					})
				} else {
					animation.transition(indEl, {
						styles: {
							width: `${this.dyIndicatorWidth}px`,
							transform: `translateX(${this.dyIndicatorLeft}px)`
						},
						duration: 200
					})
				}
				// #endif
				// ????????????item
				const scrollWidth = this.scrollSizePx
				const offset = _width - (nowWidth / 2 + this.spacePx) - scrollWidth / 2
				if (offset > 0) {
					// app???scrollTo????????????????????????????????????????????????????????????????????????????????????????????????
					// ??????????????????dom.scrollToElement. dom.scrollToElement?????????????????????
					// #ifdef APP-NVUE
					const refName = "item" + index
					const el = this.$refs[refName][0]
					dom.scrollToElement(el, {
						offset: -0.5 * scrollWidth + nowWidth * 0.5,
						animated: true
					})
					// #endif
					// #ifndef APP-NVUE
					this.scrollLeft = offset;
					// #endif
				} else {
					if (this.scrollLeft === 0) {
						this.scrollLeft = 0.1
					} else {
						this.scrollLeft = 0;
					}
				}
			},
			async toHandleSwiperScroll(dx) {
				// ???mp-wx??????scroll????????????????????????????????????current??????????????????????????????this.value??????????????????tab
				// const nowIndex = this.value
				const nowIndex = this.last
				let nextIndex = nowIndex
				// ?????? 1px ????????????
				if (dx > -1 && dx < 1) return;
				if (dx > 0) {
					nextIndex += 1
				} else if (dx < 0) {
					nextIndex -= 1
				}
				const pg = Math.abs(this.swiperWidthPx / dx)
				if (nextIndex>=this.items.length || nextIndex < 0 || nowIndex === nextIndex) {
					return
				}
				const theItem = this.dyItems['item'+nowIndex]
				const nextItem = this.dyItems['item'+nextIndex]
				
				this.dyIndicatorLeft = theItem.indLeft + (nextItem.indLeft - theItem.indLeft) / pg
				this.dyIndicatorWidth = theItem.ind + (nextItem.ind - theItem.ind) / pg
				
				// #ifdef APP-NVUE
				const indEl = this.$refs['n-underline']
				animation.transition(indEl, {
					styles: {
						width: `${this.dyIndicatorWidth}px`,
						transform: `translateX(${this.dyIndicatorLeft}px)`
					}
				})
				// #endif
			},
			async toCacheItemsSize() {
				let scrollL = 0
				try{
					const res = await this.getElSize(-100)
					scrollL = res.left
					this.dyItems['scroll'] = {left: scrollL}
				}catch(e){
					//TODO handle the exception
				}
				for (const i in this.items) {
					try{
						const result = await this.getElSize(i)
						let indWidth = result.width
						if (this.fixedInd) {
							indWidth = this.fixedIndWidthPx
						}
						this.dyItems['item'+i] = {width:result.width, left: result.left-scrollL, ind: indWidth, indLeft: result.left-scrollL+(result.width-indWidth)*0.5}
					}catch(e){
						//TODO handle the exception
					}
				}
			},
			getElSize(index) {
				return new Promise((res, rej) => {
					// #ifndef APP-NVUE
					let idName = '#item' + index
					if (index===-100) {
						idName = "#scroll"
					}
					const el = uni.createSelectorQuery().in(this).select(idName)
					el.fields({
						size: true,
						rect: true
					}, (data) => {
						if (data) {
							res(data);
						} else {
							rej({})
						}
					}).exec();
					// #endif
					// #ifdef APP-NVUE
					let refName = "item" + index
					let _el = null
					if(index===-100) {
						_el = this.$refs["scroll"]
					} else {
						_el = this.$refs[refName][0]
					}
					dom.getComponentRect(_el, (data) => {
						if (data.result) {
							res(data.size)
						} else {
							rej({})
						}
					})
					// #endif
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	
	/* #ifndef APP-NVUE */
	.n-tabs-scroll ::-webkit-scrollbar {
	    display: none;
	    width: 0 !important;
	    height: 0 !important;
		color: transparent;
	    -webkit-appearance: none;
	    background: transparent;
	}
	/* #endif */
	
	.n-tab-item {
		&-underline {
			position: absolute;
			bottom: 0;
			left: 0;
			width: 0;
		}
		&-animation {
			/* #ifndef APP-NVUE */
			transition-property: left, width;
			transition-duration: 0.2s;
			transition-timing-function: ease;
			/* #endif */
		}
		&-noani {
			transition-duration: 0s;
		}
	}
</style>
