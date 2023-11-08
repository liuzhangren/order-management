<template>
	<view class="CaculateContainer">
		<view class="CaculateContainer-box">
			<form>
				<view class="form-item">
					<view class="form-item-label">铜章尺寸</view>
					<view class="form-item-content">
						<picker
							@change="bindPickerChange('size', $event)"
							:value="sizeIndex ? sizeIndex : ''"
							:range="sizeArray"
						>
							<view class="uni-input">{{ sizeIndex ? sizeArray[sizeIndex] : '请选择铜章尺寸' }}</view>
						</picker>
					</view>
				</view>
				
				<view class="form-item">
					<view class="form-item-label">异形状</view>
					<view class="form-item-content">
						<picker
							@change="bindPickerChange('yx', $event)"
							:value="yxIndex ? yxIndex : ''"
							:range="yxArray"
						>
							<view class="uni-input">{{ yxIndex ? yxArray[yxIndex] : '请选择异形状' }}</view>
						</picker>
					</view>
				</view>
				<view class="form-item">
					<view class="form-item-label">基础雕刻工艺(必选)</view>
					<view class="form-item-content flex-right">
						<switch @change="baseChange" />
					</view>
				</view>
				<view v-if="showBase" class="basic-form">
					<view
						v-for="item in basicDataArray"
						:key="item.text"
						@tap="basicCheckedChange(item)"
					>
						<checkbox-group>
							<view class="form-item">
								<checkbox :value="item.text" :checked="item.checked"></checkbox>
								<view>{{ item.text }}</view>
							</view>
						</checkbox-group>
						<view v-if="item.checked" class="image-box">
							<image
								@tap.stop="previewImage(item.src)"
								class="image"
								mode="heightFix"
								:src="item.src"
							></image>
						</view>
					</view>
				</view>
				<view class="form-item">
					<view class="form-item-label">附加工艺(非必选)</view>
					<view class="form-item-content flex-right">
						<switch @change="additionalChange" />
					</view>
				</view>
				<view v-if="showAdditional" class="basic-form">
					<view
						v-for="(item, index) in additionalDataArray"
						:key="item.text"
						@tap="additionalCheckedChange(item)"
					>
						<checkbox-group>
							<view class="form-item">
								<checkbox :value="item.text" :checked="item.checked"></checkbox>
								<view>{{ item.text }}</view>
							</view>
						</checkbox-group>
						<view v-if="additionalCheckedItems.includes(item.text)" class="image-box">
							<image
								@tap.stop="previewImage(item.src)"
								class="image"
								mode="heightFix"
								:src="item.src"
							></image>
						</view>
					</view>
				</view>
				<view class="form-item">
					<view class="form-item-content" style="font-size: 12px;">
						做货数量
						<span style="color: red;">(批量做货数量大于10小于45件，价格请咨询客服)</span>
					</view>
				</view>
				<view class="form-item">
					<view class="form-item-content">
						<picker
							@change="bindPickerChange('count', $event)"
							:value="countIndex ? countIndex : ''"
							:range="countArray"
						>
							<view class="uni-input">{{ countIndex ? countArray[countIndex] : '请选择做货数量' }}</view>
						</picker>
					</view>
				</view>
				<view class="form-item">
					<button @tap="submit" class="submit-btn" type="primary">计算价格</button>
				</view>
			</form>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			this.sizePriceMap = {
				打样: {
					12: 5,
					15: 5,
					20: 7,
					22: 8,
					25: 9,
					30: 10,
					35: 11,
					40: 12,
					45: 15,
					50: 18,
				},
				大货: {
					12: 5,
					15: 5,
					20: 7,
					22: 8,
					25: 9,
					30: 10,
					35: 11,
					40: 12,
					45: 15,
					50: 18,
				}
			}
			this.yxPriceMap = {
				打样: {
					异形: 15,
					无异形: 0
				},
				大货: {
					异形: 10,
					无异形: 0
				}
			}
			this.basicPriceMap = {
				打样: {
					单层普通平雕: 20,
					多层雕刻: 30,
					浮雕: 25
				},
				大货: {
					单层普通平雕: 8,
					多层雕刻: 15,
					浮雕: 18
				}
			}
			this.additionalPriceMap = {
				打样: {
					直刻: 15,
					勾线: 20,
					精雕: 30,
					微浮雕: 10,
					阳刻: 20,
					粗磨砂: 15,
					细磨砂: 20,
					海波纹: 15,
					布纹: 15,
					华夫格: 20,
					喷砂: 8,
					镜面: 40
				},
				大货: {
					直刻: 10,
					勾线: 14,
					精雕: 20,
					微浮雕: 8,
					阳刻: 15,
					粗磨砂: 10,
					细磨砂: 12,
					海波纹: 10,
					布纹: 10,
					华夫格: 15,
					喷砂: 5,
					镜面: 25
				}
			}
			return {
				sizeIndex: null,
				sizeArray: ['12', '15', '20', '22', '25', '30', '35', '40', '45', '50'],
				yxArray: ['异形', '无异形'],
				yxIndex: null,
				countArray: ['打样', '二次打样', '大货'],
				countIndex: null,
				showBase: false,
				showAdditional: false,
				basicDataArray: [
					{
						text: '单层普通平雕',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
					{
						text: '多层雕刻',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
					{
						text: '浮雕',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
				],
				additionalCheckedItems: [],
				basicCheckedItems: [],
				additionalDataArray: [
					{
						text: '直刻',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
					{
						text: '勾线',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
					{
						text: '精雕',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
					{
						text: '微浮雕',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
					{
						text: '阳刻',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
					{
						text: '粗磨砂',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
					{
						text: '细磨砂',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
					{
						text: '海波纹',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
					{
						text: '布纹',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
					{
						text: '华夫格',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
					{
						text: '喷砂',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
					{
						text: '镜面',
						checked: false,
						src: 'https://qny.haowen.city/awt1/wx/20231107/575cb415477c4081b19b6f155769e248.png'
					},
				]
			}
		},
		onLoad() {

		},
		methods: {
			previewImage(url) {
				uni.previewImage({
					urls: [url],
					current: url
				})
			},
			baseChange(e) {
				this.showBase = e.target.value
			},
			additionalChange(e) {
				this.showAdditional = e.target.value
			},
			bindPickerChange(type, e) {
				this[`${type}Index`] = e.detail.value
			},
			additionalCheckedChange(item) {
				// console.log('### additionalCheckedChange', e)
				// this.additionalCheckedItems = this.additionalCheckedItems.concat(e.target.value)
				this.additionalDataArray = this.additionalDataArray.reduce((r, c) => {
					if (c.text === item.text) {
						c.checked = !c.checked
					}
					r.push(c)
					return r
				}, [])
			},
			basicCheckedChange(item) {
				this.basicDataArray = this.basicDataArray.reduce((r, c) => {
					if (c.text === item.text) {
						c.checked = !c.checked
					}
					r.push(c)
					return r
				}, [])
			},
			submit() {
				const sizeValue = this.sizeArray[this.sizeIndex]
				const yxValue = this.yxArray[this.yxIndex]
				const basicValue = this.basicDataArray.filter(item => item.checked).map(item => item.text)
				const addtionalValue = this.additionalDataArray.filter(item => item.checked).map(item => item.text)
				console.log(basicValue, addtionalValue)
				let countValue = this.countArray[this.countIndex]
				if (!sizeValue) {
					uni.showModal({
						content: '请选择铜章尺寸',
						duration: 2000,
						showCancel:false,
						confirmText:'确定'
					})
					return
				} else if (!yxValue) {
					uni.showModal({
						content: '请选择是否异形',
						duration: 2000,
						showCancel:false,
						confirmText:'确定'
					})
					return
				} else if (!basicValue.length) {
					uni.showModal({
						content: '请选择基础工艺',
						duration: 2000,
						showCancel:false,
						confirmText:'确定'
					})
					return
				} else if (!countValue) {
					uni.showModal({
						content: '请选择做货数量',
						duration: 2000,
						showCancel:false,
						confirmText:'确定'
					})
					return
				}
				let flag = ''
				if (countValue === '二次打样') {
					flag = '二次打样'
					countValue = '打样'
				}
				const sizePrice = this.sizePriceMap[countValue][sizeValue],
					yxPrice = this.yxPriceMap[countValue][yxValue],
					basicPrice = basicValue.reduce((r, c) => {
						r = r + this.basicPriceMap[countValue][c]
						return r
					}, 0),
					additionalPrice = addtionalValue.reduce((r, c) => {
						r = r + this.additionalPriceMap[countValue][c]
						return r
					}, 0);
					// console.log(this.sizePriceMap[countValue], sizeValue)
					const totalPrice = sizePrice + yxPrice + additionalPrice + basicPrice
					uni.showModal({
						title:'货物价格',
						content: `计算价格为${flag ? totalPrice * 0.85 : totalPrice}元`,
						showCancel:false,
						confirmText:'确定'
					})
			},
		}
	}
</script>

<style lang="scss" scoped>
	.CaculateContainer {
		height: 100vh;
		&-box {
			// padding: 20px;
			position: relative;
			.image-box {
				padding: 0 20px;
				display: flex;
				justify-content: center;
			}
			.form-item {
				display: flex;
				align-items: center;
				justify-content: space-between;
				height: 40px;
				padding: 0 12px;
				border: 1px solid #eee;
				&-label {
					// width: 120px;
				}
				&-content {
					flex: 1;
				}
				.flex-right {
					display: flex;
					justify-content: flex-end;
				}
				.submit-btn {
					margin-top: 12px;
					width: 100%;
				}
			}
		}
	}
</style>
