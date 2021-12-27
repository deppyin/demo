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
			<view class="list">
				<ul>
					<li class="item" v-for="(item,index) in list" :key="index" @click="show = true">
						<text>{{item.name}}</text>
						<u-icon name="arrow-right" color="#ccc" size="20"></u-icon>
					</li>
					<li class="introduction" @click="introduction">
						<text>退出登陆</text>
					</li>
				</ul>
			</view>

			<u-popup :show="show" mode="center">
				<view class="pop">
					<view class="popcontent">
						<view class="title">
							<u-icon name="close" color="#ccc" size="20" @click="show=false"></u-icon>
						</view>

						<view class="className">
							菜单名称
							<!--@blur="menuListShow=false"  -->
							<u--input placeholder="菜单名称" border="surround" v-model="className" @focus="getMenuList" >
							</u--input>
							<view class="menuList" v-show="menuListShow" v-if="className == ''">
								<ul>
									<li :class="className==item.class_name?'item1':'item'" v-for="(item,index) in menuList" :key="index" @click.stop="menuItem(item.class_name)">{{item.class_name}}</li>
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
							<u-upload :fileList="fileList1" @afterRead="afterRead" multiple :maxCount="1"></u-upload>
						</view>
						<u-button type="primary" text="提交" style="width: 80%;" @click="submit"></u-button>
					</view>
				</view>
			</u-popup>
		</view>
	</view>
</template>

<script>
	import defaultHeader from "@/static/image/4465b49d9a4d7a60d751d2c15825c9e2.jpeg"
	export default {
		data() {
			return {
				userinfo: {},
				src: defaultHeader,
				list: [{
					name: "商品上架"
				}, ],
				show: false,
				shopName: "",
				className: "",
				fileList1: [],
				describe: "",
				price: 0,
				menuList: [],
				menuListShow:false
			};
		},
		mounted() {
			let userinfo = uni.getStorageSync("userinfo")
			userinfo = JSON.parse(userinfo)
			this.userinfo = userinfo
		},
		methods: {
			afterRead(file, lists, name) {
				console.log(file)
			},
			submit() {
				uni.request({
					url: baseUrl+"/shop/pushShopList",
					method: "post",
					data: JSON.stringify({
						id: this.userinfo.id,
						shop_name: this.shopName,
						class_name: this.className,
						token: this.userinfo.token,
						describe: this.describe,
						price: this.price
					}),
					success: function(res) {
						uni.showToast({
							title: res.data.msg + "",
						});
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
					url: baseUrl+"/shop/getShopMenu",
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
			menuItem(item){
				console.log(item)
				this.className = item
			}
		}
	}
</script>

<style lang="scss">
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
			margin-bottom: 20px;
			width: 100%;

			.title {
				margin-top: 20px;
				width: 100%;
				display: flex;
				justify-content: flex-end;
				margin-right: 30px;
			}

			.className {
				margin-top: 20px;
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
						.item1{
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
			}
		}

	}

	.userMain {
		height: 100%;
		margin: auto;


		.header {
			display: flex;
			height: 20%;
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
		}
	}
</style>
