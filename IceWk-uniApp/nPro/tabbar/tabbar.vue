<template>
	<view class="n-tab-page n-flex-column" :style="{top: topPx+'px'}">
		<view class="n-tab-page-container n-flex-row" ref="tab-container" :style="mrTabContainerStyle+noWeexTransform">
			<slot></slot>
		</view>
		<view v-if="isSeize">
			<view :style="{height: tabHeightPx + 'px'}"></view>
			<n-height height="x" v-if="considerXBar" bgType="none"></n-height>
		</view>
		<!-- tabs bg -->
		<slot name="bg">
			<view v-if="tabStyle.image" class="n-tabs-img" :style="tabStyle.imageBoxStyle||''">
				<image :src="tabStyle.image" :style="tabStyle.imageStyle||''" mode="aspectFill"></image>
				<n-height height="x" v-if="considerXBar&&!tabStyle.imageWidthXBar" :bgType="xBarBgType" :boxStyle="xBarStyle"></n-height>
			</view>
		</slot>
		<slot name="tabs">
			<view class="n-tabs n-flex-column" :style="tabStyle.boxStyle||''">
				<view class="n-tabs-items n-flex-row" :style="tabStyle.tabsStyle||''">
					<view bubble="true" class="n-tabs-item n-flex-column n-flex-one n-align-center n-justify-between" v-for="(item, idx) in tabs" :key="idx" :ref="'n-tab-'+idx" :style="mrItemStyle" @tap="setPage(idx)">
						<view v-if="!item.isHump" class="n-tabs-item-icon n-flex-row n-justify-center n-align-center" :style="'width:'+(item.iconBoxWidth||mrIconWidth)+';'">
							<image :src="currentPage===idx?item.selectedIcon:item.icon" :style="currentPage===idx?((tabStyle.selectedIconStyle||'')+(item.selectedIconStyle||'')):((tabStyle.iconStyle||'')+(item.iconStyle||''))"></image>
							<view v-if="item.badge" class="n-tabs-item-badge" :style="(tabStyle.badgeStyle||'')+(item.badgeStyle||'')">
								<text class="n-tabs-item-badge-text" :style="(tabStyle.badgeTextStyle||'')+(item.badgeTextStyle||'')">{{item.badge}}</text>
							</view>
							<view v-if="item.dot && !item.badge" class="n-tabs-item-dot" :style="(tabStyle.dotStyle||'')+(item.dotStyle||'')"></view>
						</view>
						<text v-if="!item.isHump" :class="['n-tabs-item-text', currentPage===idx&&'n-tabs-item-text-selected']" :style="currentPage===idx?((tabStyle.selectedTitleStyle||'')+(item.selectedTitleStyle||'')):((tabStyle.titleStyle||'')+(item.titleStyle||''))">{{currentPage===idx ? item.selectedTitle : item.title}}</text>
					</view>
				</view>
				<n-height height="x" v-if="considerXBar&&!tabStyle.imageWidthXBar" :bgType="xBarBgType" :boxStyle="xBarStyle"></n-height>
			</view>
			<!-- hump -->
			<view bubble="true" v-if="hasHump" class="n-tabs-hump n-flex-column n-align-center n-justify-center" :style="mrHumpStyle||''" @tap="setPage(humpIndex)">
				<view class="n-tabs-item-icon n-flex-row n-justify-center n-align-center" :style="'width:'+(humpItem.iconBoxWidth||mrIconWidth)+';'">
					<image :src="currentPage===humpIndex?humpItem.selectedIcon:humpItem.icon" :style="currentPage===humpIndex?((tabStyle.selectedIconStyle||'')+(humpItem.selectedIconStyle||'')):((tabStyle.iconStyle||'')+(humpItem.iconStyle||''))"></image>
					<view v-if="humpItem.badge" class="n-tabs-item-badge" :style="(tabStyle.badgeStyle||'')+(humpItem.badgeStyle||'')">
						<text class="n-tabs-item-badge-text" :style="(tabStyle.badgeTextStyle||'')+(humpItem.badgeTextStyle||'')">{{humpItem.badge}}</text>
					</view>
					<view v-if="humpItem.dot && !humpItem.badge" class="n-tabs-item-dot" :style="(tabStyle.dotStyle||'')+(humpItem.dotStyle||'')"></view>
				</view>
				<text v-if="humpItem.title" :class="['n-tabs-item-text', currentPage===humpIndex&&'n-tabs-item-text-selected']" :style="currentPage===humpIndex?((tabStyle.selectedTitleStyle||'')+(humpItem.selectedTitleStyle||'')):((tabStyle.titleStyle||'')+(humpItem.titleStyle||''))">{{currentPage===humpIndex ? humpItem.selectedTitle : humpItem.title}}</text>
			</view>
		</slot>
	</view>
</template>

<script>
	/**
	 * tabbar
	 * @description ?????????tabbar
	 * @property {Number} duration ???????????????????????????
	 * @property {String} timingFunction ???????????????????????????
	 * @property {String} top ?????????????????????
	 * @property {Boolean} isSeize tabs??????xbar????????????????????????
	 * @property {Boolean} type considerXBar
	 * @property {String} xBarBgType xbar???????????????
	 * @property {String} xBarStyle xbar?????????
	 */
	// #ifdef APP-NVUE
	const animation = uni.requireNativePlugin('animation');
	// #endif
	
	import {getPx, getXBarHeight, getHeight, getStatusBarHeight, getNavBarHeight} from '../utils/system.js'
	
	export default {
		props: {
			/**
			 * tabs????????????????????????????????????
			 * isHump??????????????????,
			 * noPage??????????????????tab?????????????????????????????????tab?????????
			 * hump??????humpStyle,hump???humpBottom(???????????????????????????px)???
			 * icon,selectedIcon,title,selectedTitle,
			 * badge,dot,
			 * isHump,noPage,humpStyle,humpBottom
			 * iconStyle,selectedIconStyle,titleStyle,selectedTitleStyle,
			 * badgeStyle,badgeTextStyle,dotStyle,iconBoxWidth
			 */
			tabs: {
				type: Array,
				default: () => ([])
			},
			/**
			 * ??????tabs??????????????????tabs?????????????????????????????????
			 */
			tabStyle: {
				type: Object,
				default: ()=>{
					return {
						iconStyle: 'font-size: 20px;color: #333232;',
						selectedIconStyle: 'font-size: 20px;color: #01A9F0;',
						titleStyle: '',
						selectedTitleStyle: '',
						boxStyle: '',
						tabsStyle: '',
						itemStyle: '',
						height: 50,  // px
						badgeStyle: '',
						badgeTextStyle: '',
						dotStyle: '',
						// we use it to help to position the badge or dot
						iconBoxWidth: '46px',
						image: null,
						imageStyle: '',
						imageBoxStyle: '',
						imageWidthXBar: false
					}
				}
			},
			/**
			 * ???????????????????????????
			 */
			duration: {
				type: Number,
				default: 300
			},
			/**
			 * ???????????????????????????
			 */
			timingFunction: {
				type: String,
				default: 'cubic-bezier(0.25, 0.46, 0.45, 0.94)'
			},
			/**
			 * ?????????????????????
			 */
			top: {
				type: String,
				default: '0'
			},
			/**
			 * tabs??????xbar????????????????????????
			 */
			isSeize: {
				type: Boolean,
				default: true
			},
			/**
			 * ????????????xbar
			 */
			considerXBar: {
				type: Boolean,
				default: true
			},
			/**
			 * xbar???????????????
			 */
			xBarBgType: {
				type: String,
				default: 'inverse'
			},
			/**
			 * xbar?????????
			 */
			xBarStyle: {
				type: String,
				default: ''
			}
		},
		data: () => ({
			currentPage: 0,
			noWeexTransform: '',
			defaultTabHeight: 50,  // px
			defaultIconBoxWidth: '46px'
		}),
		computed: {
			mrHumpStyle() {
				if (!this.humpItem) return '';
				let btm = this.humpItem.humpBottom || 12
				if (this.considerXBar) {
					btm += getXBarHeight()
				}
				const style = this.humpItem.humpStyle || ''
				return style + `bottom:${btm}px;`
			},
			hasHump() {
				for (const i in this.tabs) {
					if (this.tabs[i].isHump) {
						return true
					}
				}
				return false
			},
			humpItem() {
				for (const i in this.tabs) {
					if (this.tabs[i].isHump) {
						return this.tabs[i]
					}
				}
				return null
			},
			humpIndex() {
				return parseInt((this.tabs.length - 1) * 0.5)
			},
			noPageIndex() {
				for (const i in this.tabs) {
					if (this.tabs[i].noPage) {
						return i*1
					}
				}
				return -1
			},
			topPx() {
				return getHeight(this.top)
			},
			tabHeightPx() {
				if (this.tabStyle && this.tabStyle.height) {
					return this.tabStyle.height // uni.upx2px(this.tabStyle.height)
				}
				return this.defaultTabHeight // uni.upx2px(this.defaultTabHeight)
			},
			mrTabContainerStyle() {
				// ??????????????????????????????
				// ???????????????????????????????????????????????????????????????
				let _length = this.tabs.length || 1
				if (this.noPageIndex >= 0) {
					_length -= 1
				}
				const width = (_length || 1) * 750
				let _style = `width:${width}rpx;`
				return _style
			},
			mrItemStyle() {
				const _height = this.tabHeightPx
				return (this.tabStyle.itemStyle||'') + `height:${_height}px;`
			},
			mrIconWidth() {
				if (this.tabStyle && this.tabStyle.iconBoxWidth) {
					return this.tabStyle.iconBoxWidth
				}
				return this.defaultIconBoxWidth
			}
		},
		methods: {
			next() {
				let page = this.currentPage;
				if (page < this.tabs.length - 1) {
					page++;
				}
				this.setPage(page);
			},
			prev() {
				let page = this.currentPage;
				if (page > 0) {
					page--;
				}
				this.setPage(page);
			},
			setPage(page, animated = true) {
				if (page === this.noPageIndex) {
					this.$emit('tabClicked', {page})
					return
				}
				this.currentPage = page;
				this._animateTransformX(page, animated);
				this.$emit('tabClicked', {
					page
				});
			},
			_animateTransformX(page, animated) {
				let fakePage = page
				if (this.noPageIndex >= 0) {
					if (page > this.noPageIndex) {
						fakePage -= 1
					}
				}
				const {
					duration,
					timingFunction
				} = this;
				const computedDur = animated ? duration : 0.00001;
				// #ifdef APP-NVUE
				const containerEl = this.$refs[`tab-container`];
				const _w = uni.upx2px(750)
				const dist = fakePage * _w;
				animation.transition(containerEl, {
					styles: {
						transform: `translateX(${-dist}px)`
					},
					duration: computedDur,
					timingFunction,
					delay: 0
				}, () => {});
				// #endif
				// #ifndef APP-NVUE
				const _tx = fakePage * 750 * -1
				this.noWeexTransform = `transition-property:transform;transition-duration:${computedDur}ms;transform:translateX(${_tx}rpx);transition-timing-function:${timingFunction};`
				// #endif
			}
		}
	};
</script>

<style lang="scss" scoped>
	.n-tab-page {
		// position: absolute;
		// ????????????fixed???
		// ??????absolute?????????mp????????????????????????.
		// ???????????????????????????0???????????????MP-IOS???isSeize??????????????????????????????????????????????????????
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		
		&-container {
			width: 750rpx;
			flex: 1;
			position: relative;
		}
	}
	.n-tabs {
		position: fixed;
		left: 0;
		bottom: 0;
		width: 750rpx;
		
		&-img {
			position: fixed;
			width: 750rpx;
			left: 0;
			bottom: 0;
		}
		&-items {
			width: 750rpx;
			// background-color: #FFFFFF;
		}
		&-hump {
			position: fixed;
			left: 375rpx;
			bottom: 0;
			transform: translateX(-50%);
		}
		&-item {
			padding: 5px;
			
			&-icon {
				position: relative;
			}
			&-text {
				font-size: 13px;
				color: #333232;
				
				&-selected {
					color: #01A9F0;
				}
			}
			&-badge {
				position: absolute;
				right: 0;
				top: -2px;
				&-text {
					color: #FF0000;
					font-size: 12px;
				}
			}
			&-dot {
				position: absolute;
				right: 6px;
				top: 2px;
				width: 6px;
				height: 6px;
				border-radius: 6px;
				background-color: #FF0000;
			}
		}
	}
</style>
