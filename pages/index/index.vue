<template>
	<view class="container">
    <view>
      <uni-segmented-control :current="currentIndex" :values="requestListTitle" @clickItem="onClickItem" styleType="text" activeColor="#2173FE"></uni-segmented-control>
    </view>
		<view class="layout">
			<view class="box" v-for="(tmp, index) in photoList"  :key="tmp._id">
				<view class="pic" @click="previewPhoto(index)">
					<image lazy-load :src="tmp.url" mode="widthFix"></image>
				</view>
				<view class="text">{{tmp.content}}</view>
				<view class="author">——{{tmp.author}}</view>
			</view>
		</view>
    <view class="loadMore">
      <uni-load-more status="loading"></uni-load-more>
    </view>
    <view class="float">
      <uni-icons class="btn" type="refreshempty" size="24" color="#888" @click="onRefesh"></uni-icons>
      <uni-icons class="btn" type="arrow-up" size="24" color="#888" @click="onTop"></uni-icons>
    </view>
	</view>
</template>

<script setup>
	import {computed, ref} from "vue";
  import {onReachBottom, onPullDownRefresh} from "@dcloudio/uni-app";
	
	const photoList = ref([]);
  const currentIndex = ref(0);
  const requestList = [{key: "all", value: "全部"}, {key: "cat", value: "喵星人"}, {key: "dog", value: "汪星人"}];
  const requestListTitle = computed(()=>requestList.map(item=>item.value));
	
	function requestPhotos(){
    // uni.showLoading();
    uni.showNavigationBarLoading();
    console.log(currentIndex.value);
		uni.request({
			url: "https://tea.qingnian8.com/tools/petShow",
			data: {
				size: 10,
        type: requestList[currentIndex.value].key
			},
			header: {
				"access-key": "smoke302"
			}
		}).then(
			res=>{
        if(res.data.errCode == 0){
          photoList.value = [...photoList.value, ...res.data.data];
        }else{
          // console.log(res);
          uni.showToast({
            title: res.data.errMsg,
            icon: "none",
            duration:3000,
            });
        }
			}
		).catch(
      err=>{
        console.log(err);
        uni.showToast({
          title: "网络错误",
          icon: "none",
          duration:3000
        });
      }
    ).finally(
      ()=>{
        // uni.hideLoading();
        uni.hideNavigationBarLoading();
        uni.stopPullDownRefresh();
      }
    )
	};
	
	function previewPhoto(index){
		uni.previewImage({
			current:index,
			urls:photoList.value.map(temp=>temp.url)
		})
	}
  
  function onClickItem(index){
    console.log(index);
    currentIndex.value = index.currentIndex;
    onRefesh();
  }
  
  onReachBottom(()=>{
    requestPhotos();
  });
  
  onPullDownRefresh(()=>{
    onRefesh();
  });
  
  function onRefesh(){
    photoList.value = [];
    requestPhotos();
  }
  
  const onTop  = () => {
    console.log("onToTop");
    uni.pageScrollTo({
      scrollTop: 0,
      success: () => {
        console.log("success");
      },
      fail: (msg) => {
        console.log("fail");
      },
      complete: () => {
        console.log("complete")
      }
    });
  }
	
	requestPhotos();
</script>

<style lang="scss" scoped>
  
	.box {
		margin: 20rpx 30rpx;
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
  .float{
    position: fixed;
    right: 20rpx;
    bottom: 20rpx;
    padding-bottom: env(safe-area-inset-bottom);
    .btn{
      background-color: rgba(255, 255, 255, 90%);
      margin-bottom: 20rpx;
      height: 100rpx;
      width: 100rpx;
      border-radius: 50rpx;
      box-shadow: 0rpx 0rpx 50px rgba(0, 0, 0, 0.3);
      display: flex;
      align-items: center;
      justify-content: center;
      border: 1rpx solid #eee;
    }
  }
</style>
