<template>
	<view class="main">

		<u-toast ref="uToast"></u-toast>

		<view class="from">
			<view class="header">
				<u-avatar :src="src" shape="circle" size="80"></u-avatar>
			</view>
			<view class="input">
				<u--input placeholder="请输入用户名" border="surround" v-model="username" shape="circle"
					style="width: 80%;margin:10px auto;"></u--input>
				<u--input placeholder="请输入密码" border="surround" v-model="password" password shape="circle"
					style="width: 80%;margin:10px auto;"></u--input>

			</view>
			<u-button type="success" text="登陆" style="width: 83%;margin-top: 10px;" shape="circle" @click="login">
			</u-button>
			<u-button type="success" :plain="true" text="注册" style="width: 83%;margin-top: 10px;" shape="circle"
				@click="register"></u-button>
		</view>
	</view>
</template>

<script>
	import defaultHeader from "@/static/image/4465b49d9a4d7a60d751d2c15825c9e2.jpeg"
	export default {
		data() {
			return {
				username: "",
				password: "",
				src: defaultHeader
			}
		},
		methods: {
			login() {
				
				uni.request({
					url: baseUrl+"/user/login",
					method: "post",
					data: JSON.stringify({
						username: this.username,
						password: this.password
					}),
					success: function(res) {
						if (res.data.code) {
							uni.showToast({
								title: res.data.msg + "",
							});
						} else {
							uni.setStorageSync("userinfo", JSON.stringify(res.data.data))
							uni.switchTab({
								url: "/pages/index/index",
							})
						}
					}
				})
			},
			register() {
				uni.request({
					url: baseUrl+"/user/register",
					method: "post",
					data: JSON.stringify({
						username: this.username,
						password: this.password,
						
					}),
					success: function(res) {
						uni.showToast({
							title: res.data.msg + "",
						});
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
		width: 100%;
		background-image: url("../../static/image/974924e442d51ad188fa9f0a9e397dc2.jpeg");
		background-repeat: no-repeat;
		background-size: 100% 100%;

		.from {
			width: 90%;
			height: 50%;
			background-color: #FFFFFF;
			position: absolute;
			left: 0;
			right: 0;
			top: 0;
			bottom: 0;
			margin: auto;
			display: flex;
			flex-direction: column;
			justify-content: center;

			.input {
				margin-top: 10px;
			}
		}

		.header {
			margin: 0 auto;
		}
	}
</style>
