<template>
	<view class="content">
		<view class="layout">
			<view class="box" v-for="(tmp, index) in photoList"  :key="tmp._id">
				<view class="pic" @click="previewPhoto(index)">
					<image lazy-load :src="tmp.url" mode="widthFix"></image>
				</view>
				<view class="text">{{tmp.content}}</view>
				<view class="author">——{{tmp.author}}</view>
			</view>
		</view>
	</view>
</template>

<script setup>
	import {ref} from "vue"
	
	const photoList = ref([]);
	
	const requestPhotos = function(){
		uni.request({
			url: "https://tea.qingnian8.com/tools/petShow",
			data: {
				size: 10
			},
			header: {
				"access-key": "smoke302"
			}
		}).then(
			res=>{
				//console.log(res);
				photoList.value = res.data.data;
				console.log(photoList.value);
			}
		)
	};
	
	const previewPhoto = function(index){
		uni.previewImage({
			current:index,
			urls:photoList.value.map(temp=>temp.url)
		})
	}
	
	requestPhotos();
</script>

<style lang="scss" scoped>
	.content {
	}
	.box {
		
		margin: 10rpx 20rpx;
		padding: 50rpx;
		box-shadow: 0px 10px 50px rgba(0, 0, 0, 0.08);
		border-radius: 30rpx;
		.pic {
			width: 100%;
		}
		image {
			width: 100%;
		}
		.text{
			padding: 10rpx 0rpx 0rpx;
			font-size: 30rpx;
			color: 0x333;
		}
		.author{
			text-align: right;
			font-size: 24rpx;
			color: 0xCCC;
		}
	}
</style>
