<script>
	export default {
		onLaunch: function() {
			var userinfo = uni.getStorageSync('userinfo')
			if (userinfo) {
				userinfo = JSON.parse(userinfo)
			} else {
				uni.navigateTo({
					url: "pages/login/login"
				})
				return
			}

			if (userinfo.token) {
				uni.navigateTo({
					url: "pages/index/index"
				})
			} else {
				uni.navigateTo({
					url: "pages/login/login"
				})
			}
			// window.baseUrl='http://47.106.152.0:3000'
			window.baseUrl='http://localhost:3000'
		},
		onShow: function() {

		},
		onHide: function() {

		}
	}
</script>

<style lang="scss">
	/*每个页面公共css */
	@import "@/uni_modules/uview-ui/index.scss";
	@import "common/demo.scss";

	* {
		margin: 0;
		padding: 0;
	}

	li {
		list-style: none;
	}
</style>
