<script>
	export default {
		onLaunch: function() {
			var userinfo = uni.getStorageSync('userinfo')
			userinfo = JSON.parse(userinfo)
			if(userinfo.token){
				uni.navigateTo({
					url:"pages/index/index"
				})
			}else{
				uni.navigateTo({
					url:"pages/login/login"
				})
			}
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
	*{
			margin: 0;
			padding: 0;
	}
	li{
		list-style: none;
	}
</style>
