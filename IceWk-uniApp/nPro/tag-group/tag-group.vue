<template>
	<view :class="['n-bg-'+bgType, 'n-border-'+border, 'n-radius-'+radius]" :style="boxStyle">
		<view v-if="column === 0" class="n-flex-row n-wrap-wrap" :style="dyBoxStyle">
			<view v-for="(item, idx) in updatedItems" :key="idx" :style="{'margin-right': idx !== updatedItems.length-1 ? columnSpace : '0', 'margin-bottom': rowSpace}">
				<n-tag :text="item[tl]" :value="item[vl]" :disabled="disabled || item[dl]" :selected="item['n-checked']" :bgType="tagBgType" :selectedBgType="tagSelectedBgType" :textType="tagTextType" :selectedTextType="tagSelectedTextType" :height="tagHeight" :textSize="tagTextSize" :border="tagBorder" :selectedBorder="tagSelectedBorder" :radius="tagRadius" :space="tagSpace" :textStyle="textStyle" :selectedTextStyle="selectedTextStyle" :disabledTextStyle="disabledTextStyle" :boxStyle="tagStyle" :selectedBoxStyle="selectedTagStyle" :disabledBoxStyle="disabledTagStyle" @tagClicked="toUpdateItemCheck(idx)"></n-tag>
			</view>
		</view>
		<view v-if="column !== 0">
			<view v-for="(rows, index) in chunkedItems" :key="index" class="n-flex-row n-wrap-nowrap" :style="{'margin-bottom': index !== chunkedItems.length-1 ? rowSpace : '0'}">
				<view v-for="(item, idx) in rows" :key="idx" class="n-flex-one" :style="{'margin-right': idx !== rows.length-1 ? columnSpace : '0'}">
					<n-tag v-if="item" :text="item[tl]" :value="item[vl]" :disabled="disabled || item[dl]" :selected="item['n-checked']" :bgType="tagBgType" :selectedBgType="tagSelectedBgType" :textType="tagTextType" :selectedTextType="tagSelectedTextType" :height="tagHeight" :textSize="tagTextSize" :border="tagBorder" :selectedBorder="tagSelectedBorder" :radius="tagRadius" :space="tagSpace" :textStyle="textStyle" :selectedTextStyle="selectedTextStyle" :disabledTextStyle="disabledTextStyle" :boxStyle="tagStyle" :selectedBoxStyle="selectedTagStyle" :disabledBoxStyle="disabledTagStyle" @tagClicked="toUpdateItemCheck(index * column + idx)"></n-tag>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	/**
	 * tag-group
	 * @description ?????????
	 * @property {Array} items ???????????????
	 * @property {Array} value ???????????????
	 * @property {Number} limits ????????????????????????0???????????????
	 * @property {Boolean} leastOne limits???1????????????????????????????????????????????????????????????????????????
	 * @property {Number} column ??????????????????tag???0???????????????????????????????????????
	 * @property {String} rowSpace ???????????????????????????
	 * @property {String} columnSpace ???????????????????????????
	 * @property {Boolean} disabled ??????????????????
	 * @property {String} textLabel ?????????????????????
	 * @property {String} valueLabel ??????????????????
	 * @property {String} disabledLabel ???????????????
	 * @property {String} bgType ????????????
	 * @property {String} border ????????????
	 * @property {String} radius ????????????
	 * @property {String} boxStyle ????????????
	 * @property {String} dyBoxStyle column???0????????????????????????
	 * @property {String} tagBgType tag???????????????
	 * @property {String} tagSelectedBgType ?????????tag???????????????
	 * @property {String} tagTextType tag?????????????????????
	 * @property {String} tagSelectedTextType ?????????tag?????????????????????
	 * @property {String} tagTextSize tag?????????????????????
	 * @property {String} tagHeight tag???????????????
	 * @property {String} tagBorder tag???????????????
	 * @property {String} tagSelectedBorder ?????????tag???????????????
	 * @property {String} tagRadius tag???????????????
	 * @property {String} tagSpace tag?????????padding
	 * @property {String} textStyle tag???????????????
	 * @property {String} selectedTextStyle ?????????tag???????????????
	 * @property {String} disabledTextStyle ?????????tag???????????????
	 * @property {String} tagStyle tag???????????????
	 * @property {String} selectedTagStyle ?????????tag???????????????
	 * @property {String} disabledTagStyle ?????????tag???????????????
	 */
	import {arrayChunk} from '../utils/utils.js'
	export default {
		props: {
			/**
			 * ???????????????
			 */
			items: {
				type: Array,
				default: ()=>{return []}
			},
			/**
			 * ???????????????
			 */
			value: {
				type: Array,
				default: ()=>{return []}
			},
			/**
			 * ????????????????????????0???????????????
			 */
			limits: {
				type: Number,
				default: 0
			},
			/**
			 * limits???1????????????????????????????????????
			 * ????????????????????????????????????
			 */
			leastOne: {
				type: Boolean,
				default: false
			},
			/**
			 * ??????????????????tag???0???????????????????????????????????????
			 */
			column: {
				type: Number,
				default: 0
			},
			/**
			 * ???????????????????????????
			 */
			rowSpace: {
				type: String,
				default: '16rpx'
			},
			/**
			 * ???????????????????????????
			 */
			columnSpace: {
				type: String,
				default: '16rpx'
			},
			/**
			 * ??????????????????
			 */
			disabled: {
				type: Boolean,
				default: false
			},
			/**
			 * ?????????????????????
			 */
			textLabel: {
				type: String,
				default: null
			},
			/**
			 * ??????????????????
			 */
			valueLabel: {
				type: String,
				default: null
			},
			/**
			 * ???????????????
			 */
			disabledLabel: {
				type: String,
				default: null
			},
			/**
			 * ????????????
			 */
			bgType: {
				type: String,
				default: ''
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
			 * ????????????
			 */
			boxStyle: {
				type: String,
				default: ''
			},
			/**
			 * column???0????????????????????????
			 */
			dyBoxStyle: {
				type: String,
				default: ''
			},
			/**
			 * tag???????????????
			 */
			tagBgType: {
				type: String,
				default: ''
			},
			/**
			 * ?????????tag???????????????
			 */
			tagSelectedBgType: {
				type: String,
				default: ''
			},
			/**
			 * tag?????????????????????
			 */
			tagTextType: {
				type: String,
				default: ''
			},
			/**
			 * ?????????tag?????????????????????
			 */
			tagSelectedTextType: {
				type: String,
				default: ''
			},
			/**
			 * tag?????????????????????
			 */
			tagTextSize: {
				type: String,
				default: ''
			},
			/**
			 * tag???????????????
			 */
			tagHeight: {
				type: String,
				default: ''
			},
			/**
			 * tag???????????????
			 */
			tagBorder: {
				type: String,
				default: 'all'
			},
			/**
			 * ?????????tag???????????????
			 */
			tagSelectedBorder: {
				type: String,
				default: 'all'
			},
			/**
			 * tag???????????????
			 */
			tagRadius: {
				type: String,
				default: 'base'
			},
			/**
			 * tag?????????padding
			 */
			tagSpace: {
				type: String,
				default: '16rpx'
			},
			/**
			 * tag???????????????
			 */
			textStyle: {
				type: String,
				default: ''
			},
			/**
			 * ?????????tag???????????????
			 */
			selectedTextStyle: {
				type: String,
				default: ''
			},
			/**
			 * ?????????tag???????????????
			 */
			disabledTextStyle: {
				type: String,
				default: ''
			},
			/**
			 * tag???????????????
			 */
			tagStyle: {
				type: String,
				default: ''
			},
			/**
			 * ?????????tag???????????????
			 */
			selectedTagStyle: {
				type: String,
				default: ''
			},
			/**
			 * ?????????tag???????????????
			 */
			disabledTagStyle: {
				type: String,
				default: ''
			}
		},
		computed: {
			tl() {
				return this.textLabel || 'title'
			},
			vl() {
				return this.valueLabel || 'value'
			},
			dl() {
				return this.disabledLabel || 'disabled'
			},
			chunkedItems() {
				if (this.column === 0) {
					return this.updatedItems
				}
				const _length = this.updatedItems.length
				const _array = arrayChunk(this.updatedItems, this.column)
				const mod = _length % this.column
				if (mod !== 0) {
					const _size = Math.ceil(_length / this.column)
					const lastIndex = _size - 1
					for (let i = 0; i < (this.column - mod); i++) {
						_array[lastIndex].push(null)
					}
				}
				return _array
			}
		},
		data() {
			return {
				updatedItems: []
			}
		},
		created() {
			this.toGenerateUpdatedItems()
		},
		watch: {
			items() {
				this.toGenerateUpdatedItems()
			}
		},
		methods: {
			toUpdateItemCheck(idx) {
				const item = this.updatedItems[idx]
				const isToCheck = !item['n-checked']
				// > 1 ?????????????????????????????????
				if (this.limits > 1 && isToCheck) {
					// ??????????????????????????????????????????v-model??????????????????????????????value?????????limits???????????????
					// ???????????????????????????????????? updatedItems???????????????????????????????????????????????????????????????value
					if (this.value && this.value.length >= this.limits) {
						this.$emit("overed")
						return
					}
				}
				// ???????????????????????????????????????????????????????????????
				if (this.limits === 1 && isToCheck && this.value.length === 1) {
					this.updatedItems.forEach(item => {
						item['n-checked'] = false
					})
				}
				if (this.limits === 1 && this.leastOne) {
					item['n-checked'] = true
				} else {
					item['n-checked'] = isToCheck
				}
				const _vals = []
				this.updatedItems.forEach(item => {
					if (item['n-checked']) {
						_vals.push(item[this.vl])
					}
				})
				// just for v-model
				this.$emit("input", _vals)
				this.$emit("change", _vals)
			},
			toGenerateUpdatedItems() {
				const upList = []
				const that = this
				this.items && this.items.forEach((item, i) => {
					// judge if checked
					const itemValue = that.valueLabel ? item[that.valueLabel] : item
					let flag = false
					for (const i in (that.value || [])) {
						const _val = that.value[i]
						if (_val === itemValue) {
							flag = true
							break
						}
					}
					if (that.textLabel) {
						upList.push({
							[that.textLabel]: item[that.textLabel],
							[that.valueLabel]: item[that.valueLabel],
							[that.disabledLabel]: item[that.disabledLabel],
							'n-checked': flag
						})
					} else {
						upList.push({
							title: item,
							value: item,
							disabled: false,
							'n-checked': flag
						})
					}
				})
				this.updatedItems = upList
			}
		}
	}
</script>

<style lang="scss" scoped>
</style>
