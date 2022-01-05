<template>
	<view class="main">
		<view class="userMain">
			<view class="header">
				<view class="userinfo">
					<u-avatar :src="src" shape="square" size="80"></u-avatar>
					<view class="info">
						<text>{{userinfo.username}}</text>
						<text></text>
					</view>
				</view>
			</view>

			<view class="pay">

				<view v-for="(item, index) in menu" :key="index">
					<image :src="item.thumb" style="width: 40px;height: 40px;"></image>
				</view>

			</view>

			<view class="list">
				<ul>
					<li class="item" v-for="(item,index) in list" :key="index" @click="navigetTo_fn(item)">
						<text>{{item.name}}</text>
						<u-icon name="arrow-right" color="#ccc" size="20"></u-icon>
					</li>
					<li class="userSet" @click="navigetTo_fn({name:'用户信息设置'})">
						<text>用户信息设置</text>
						<u-icon name="arrow-right" color="#ccc" size="20"></u-icon>
					</li>
					<li class="introduction" @click="introduction">
						<text>退出登陆</text>
					</li>
				</ul>
			</view>

			<u-popup :show="show" mode="center">
				<!-- <view class="pop"> -->
				<scroll-view class="pop" :scroll-y="true">
					<view class="popcontent">
						<view class="title">
							<u-icon name="close" color="#ccc" size="20" @click="show=false" style="margin-right: 3px">
							</u-icon>
						</view>
						<view class="className">
							菜单名称
							<!--@blur="menuListShow=false"  -->
							<u--input placeholder="菜单名称" border="surround" v-model="className" @focus="getMenuList">
							</u--input>
							<view class="menuList" v-show="menuListShow" v-if="className == ''">
								<ul>
									<li :class="className==item.class_name?'item1':'item'"
										v-for="(item,index) in menuList" :key="index"
										@click.stop="menuItem(item.class_name)">{{item.class_name}}</li>
								</ul>
							</view>
						</view>
						<view class="shopName">
							商品名称
							<u--input placeholder="商品名称" border="surround" v-model="shopName">
							</u--input>
						</view>
						<view class="describe">
							商品描述
							<u--input placeholder="商品描述" border="surround" v-model="describe">
							</u--input>
						</view>
						<view class="describe">
							商品价格
							<u--input placeholder="商品价格" border="surround" v-model="price">
							</u--input>
						</view>
						<view class="upload">
							商品图片
							<u-upload @afterRead="afterRead" multiple :maxCount="1" v-show="fileList1.length<=0">
							</u-upload>
							<image v-for="(item,index) in fileList1" :key="index" :src="item" mode=""
								style="height: 80px;width: 80px;margin-bottom: 10px;"></image>
						</view>
						<u-button type="primary" text="提交" style="width: 80%;" @tap="submit"></u-button>
					</view>
				</scroll-view>
				<!-- </view> -->
			</u-popup>
		</view>
	</view>
</template>

<script>
	import defaultHeader from "@/static/image/4465b49d9a4d7a60d751d2c15825c9e2.jpeg"
	import gouwuche from "@/static/image/gouwuche.png"
	import kabao from "@/static/image/kabao.png"

	export default {
		data() {
			return {
				userinfo: {},
				src: defaultHeader,
				list: [{
					name: "商品新增"
				}, {
					name: "商品管理"
				}, {
					name: "下架商品"
				}, {
					name: "热门商品"
				}],
				show: false,
				shopName: "",
				className: "",
				fileList1: [],
				describe: "",
				price: 0,
				menuList: [],
				menuListShow: false,
				menu: [{
						thumb: kabao
					},
					{
						thumb: gouwuche
					},
				]
			};
		},
		mounted() {
			let userinfo = uni.getStorageSync("userinfo")
			userinfo = JSON.parse(userinfo)
			this.userinfo = userinfo
			this.src = userinfo.head_portrait
			console.log(this.src)
		},
		methods: {
			afterRead(file, lists, name) {
				console.log("img", file.file[0])
				// var formData = new FormData();
				// formData.append("img",file.file[0])
				var that = this
				uni.uploadFile({
					url: this.$baseUrl + "/upload/imgUpload",
					filePath: file.file[0].thumb,
					name: 'img',
					success: function(res) {
						var data = JSON.parse(res.data)
						uni.showToast({
							title: res.data.msg + "",
						});

						that.fileList1.push(data.data.url)
					}
				})
			},
			submit() {
				console.log(11111111111111111111111)
				var that = this
				uni.request({
					url: this.$baseUrl + "/shop/pushShopList",
					method: "post",
					data: JSON.stringify({
						id: this.userinfo.id,
						shop_name: this.shopName,
						class_name: this.className,
						token: this.userinfo.token,
						describes: this.describe,
						price: this.price,
						shop_image: this.fileList1[0]
					}),
					success: function(res) {
						uni.showToast({
							title: res.data.msg + "",
						});
						that.show = false
					}
				})
			},
			introduction() {
				uni.clearStorageSync()
				uni.navigateTo({
					url: "/pages/login/login"
				})
			},
			getMenuList() {
				var that = this
				uni.request({
					url: this.$baseUrl + "/shop/getShopMenu",
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
							that.menuList = res.data.data
							that.menuListShow = true;
						}
					}
				})
			},
			menuItem(item) {
				console.log(item)
				this.className = item
			},
			navigetTo_fn(item) {
				console.log(item.name)
				switch (item.name) {
					case "商品新增":
						this.show = true
						console.log(this.show)
						break;
					case "商品管理":
						uni.navigateTo({
							url: '/pages/Administration/Administration?id=' + this.userinfo.id
						})
						break;
					case "下架商品":
						uni.navigateTo({
							url: '/pages/outShop/outShop?id=' + this.userinfo.id
						})
						break;
					case "用户信息设置":
						uni.navigateTo({
							url: '/pages/userSetPage/userSetPage'
						})
						break;
					case "热门商品":
						uni.navigateTo({
							url: '/pages/hotList/hotList'
						})
						break;
				}
			}
		}
	}
</script>

<style lang="scss">
	// * {
	//    touch-action: none;
	// }

	uni-page-body {
		height: 100%;
	}

	.main {
		background-color: WhiteSmoke;
		height: 100%;
	}

	/deep/ .u-popup__content {
		border-radius: 10px;
	}

	.pay {
		background-color: #fff;
		margin: 20px 0;
		display: flex;
		justify-content: space-around;
		width: 100%;
		height: 80px;
		align-items: center;
	}

	.pop {
		height: 400px;
		width: 300px;


		overflow: auto;

		.popcontent {
			display: flex;
			flex-direction: column;
			align-items: center;
			font-size: 15px;
			color: #000;
			padding-bottom: 20px;
			width: 100%;
			overflow: auto;

			.title {
				width: 300px;
				height: 40px;
				display: flex;
				justify-content: flex-end;
				position: fixed;
				background-color: #fff;
				z-index: 9999;
				border-radius: 15px
			}

			.className {
				margin-top: 40px;
				width: 80%;
				position: relative;

				.menuList {
					width: 99%;
					height: 100px;
					position: absolute;
					background-color: #fff;
					z-index: 99;
					border: 1px solid #dadbde;
					overflow: auto;

					ul {
						width: 100%;

						.item {
							width: 100%;
							font-size: 15px;
							text-align: center;
							border-bottom: 1px solid #ccc;
							height: 30px;
							line-height: 30px;

						}

						.item1 {
							width: 100%;
							font-size: 15px;
							text-align: center;
							border-bottom: 1px solid #ccc;
							height: 30px;
							line-height: 30px;
							background-color: #BBBCBE;
						}
					}
				}
			}

			.shopName {
				margin-top: 20px;
				width: 80%;
			}

			.describe {
				margin-top: 20px;
				width: 80%;
			}

			.upload {
				margin-top: 20px;
				width: 80%;
				display: flex;
				flex-direction: column;
			}
		}

	}

	.userMain {
		height: 100%;
		margin: auto;


		.header {
			display: flex;
			height: 170px;
			background-color: #fff;

			.userinfo {
				display: flex;
				// justify-content: space-between;
				align-items: flex-start;
				align-items: center;
				width: 100%;
				padding: 0 20px;

				.info {
					display: flex;
					height: 80px;
					flex-direction: column;
					margin-left: 10px;
				}
			}
		}

		.list {
			margin-top: 20px;

			.item {
				font-size: 15px;
				background-color: #fff;
				height: 44px;
				width: 90%;
				// line-height: 44px;
				padding: 0 20px;
				border-bottom: 1px solid WhiteSmoke;
				display: flex;

				justify-content: space-between;
				align-items: center;
			}

			.introduction {
				font-size: 15px;
				background-color: #fff;
				height: 44px;
				width: 90%;
				// line-height: 44px;
				padding: 0 20px;
				border-bottom: 1px solid WhiteSmoke;
				display: flex;

				justify-content: center;
				align-items: center;
				color: red;
				position: fixed;
				bottom: 60px;
			}

			.userSet {
				font-size: 15px;
				background-color: #fff;
				height: 44px;
				width: 90%;
				padding: 0 20px;
				border-bottom: 1px solid WhiteSmoke;
				display: flex;
				margin-top: 10px;
				justify-content: space-between;
				align-items: center;
			}
		}
	}
</style>
