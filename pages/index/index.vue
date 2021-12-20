<template>
	<view class="main">
		<view class="shopNav">
			<ul>
				<li class="item" v-for="(item,index) in shopNavList" :key="index"></li>
			</ul>
		</view>
		<view class="shopList">
			<ul>
				<li class="item" v-for="(item,index) in shopList" :key="index"></li>
			</ul>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				shopNavList:[],
				shopList:[]
			}
		},
		methods: {

		}
	}
</script>

<style lang="scss">
	uni-page-body{
		height: 100%;
	}
	.main {
		height: 100%;
		display: flex;
		.shopNav {
			flex: 1;
			background-color: #F3F4F6;
			.icon{
				text-align: center;
			}
		}
		.shopList {
			flex: 2;
		}
	}
</style>
