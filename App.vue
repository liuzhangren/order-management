<script>
	export default {
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		},
		globalData: {
			test: ''
		},
	}
</script>

<style lang="scss">
	@import '@/uni_modules/uni-scss/index.scss';
	@import './common/uni.css';
</style>
