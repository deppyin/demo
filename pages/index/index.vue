<template>
	<view class="main">
		<view class="shopNav">
			<ul>
				<li :class="currentClass == index?'hoverItem':'item'" v-for="(item,index) in shopNavList" :key="index"
					@click="changeSelect(index,item.class_name)">{{item.class_name}}</li>
			</ul>
		</view>
		<view class="shopList">
			<ul>
				<li class="item" v-for="(item,index) in shopList" :key="index" v-show="item.shop_status == 1">
					<image class="img" :src="item.shop_image" mode=""></image>
					<view class="shopInfo">
						<text>名称：{{item.shop_name}}</text>
						<text>描述：{{item.describes}}</text>
						<text style="color: red;margin-top: 10px;
				    display: flex;
				    align-items: flex-end;
				    height: 40px;
				">价格：{{item.price}}</text>
					</view>
				</li>
			</ul>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				shopNavList: [],
				shopList: [],
				userinfo: {},
				currentClass: 0
			}
		},
		mounted() {
			let userinfo = uni.getStorageSync("userinfo")
			if (userinfo) {
				userinfo = JSON.parse(userinfo)
			} else {
				uni.navigateTo({
					url: "pages/login/login"
				})
				return
			}
			this.userinfo = userinfo
			var that = this
			uni.request({
				url: this.$baseUrl + '/shop/getShopMenu',
				method: "post",
				data: JSON.stringify({
					id: this.userinfo.id,
					token: this.userinfo.token
				}),
				success: function(res) {
					if (res.data.code) {
						uni.showToast({
							title: res.data.msg + "",
						});
						console.log(res.data.code)
						if (res.data.code == 1000) {
							uni.navigateTo({
								url: "/pages/login/login"
							})
						}
					} else {
						that.shopNavList = res.data.data
						that.changeSelect(0, res.data.data[0].class_name)
					}
				}
			})
		},
		methods: {
			changeSelect(data, name) {
				this.currentClass = data
				// getShopMenu
				var that = this
				uni.request({
					url: this.$baseUrl + "/shop/getShopList",
					method: "post",
					data: JSON.stringify({
						id: this.userinfo.id,
						token: this.userinfo.token,
						class_name: name
					}),
					success: function(res) {
						if (res.data.code) {
							uni.showToast({
								title: res.data.msg + "",
							});
							console.log(res.data.code)
							if (res.data.code == 1000) {
								uni.navigateTo({
									url: "/pages/login/login"
								})
							}
						} else {
							that.shopList = res.data.data

						}
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	uni-page-body {
		height: 100%;
	}

	.main {
		height: 100%;
		display: flex;

		.shopNav {
			flex: 1;
			background-color: #F3F4F6;

			.item {
				height: 44px;
				line-height: 44px;
				border-bottom: 1px solid LightSeaGreen;
				font-size: 15px;
				text-align: center;
				white-space: nowrap;
				text-overflow: ellipsis;
				overflow: hidden;

			}

			.hoverItem {
				height: 44px;
				line-height: 44px;
				border-bottom: 1px solid LightSeaGreen;
				font-size: 15px;
				text-align: center;
				white-space: nowrap;
				text-overflow: ellipsis;
				overflow: hidden;
				background-color: LightSeaGreen;
				color: #fff;
			}
		}

		.shopList {
			flex: 2;

			.item {
				width: 90%;
				height: 100px;
				margin: 0 auto;
				background-color: Snow;
				display: flex;
				margin-top: 10px;
				align-items: center;

				.shopInfo {
					font-size: 15px;
					color: DimGray;
					height: 90px;
					margin-left: 5px;
					display: flex;
					flex-direction: column;
				}

				.img {
					width: 90px;
					height: 90px;
					border: 1px solid #000;

				}
			}
		}
	}
</style>
