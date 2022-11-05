<template>
    <view class="user">
        <view class="top">
            <image src="../../static/lishi.png" mode=""></image>
            <view class="title">浏览历史</view>
        </view>
        <view class="content">
            <view class="row" v-for="item in listArr">
                 <newsbox :item="item" @click.native="gotoDetail(item)"></newsbox>
            </view>
        </view>
        
        <view class="nohistory" v-if="!listArr.length">
            <image src="../../static/nodata.png" mode="widthFix" alt="">
            <view class="text">
                暂无浏览记录
            </view>
        </view>
    </view>
</template>

<script>
    export default {
        data() {
            return {
                listArr: []
            };
        },
        methods: {
            gotoDetail(item) {
                uni.navigateTo({
                    url: `/pages/detail/detail?cid=${item.classid}&id=${item.id}`
                })
            },
            //获取缓存数据
            getHisData() {
                let hisArr = uni.getStorageSync("historyArr") || []
                this.listArr = hisArr
                console.log(this.listArr);
            }
        },
        onShow() {
            this.getHisData()
        }
    }
</script>

<style lang="scss">
    .user {
        .top {
            // display: flex;
            // justify-content: center;
            text-align: center;
            padding: 50rpx 0;
            background: #f8f8f8;
            color: #555;
            image {
                width: 150rpx;
                height: 150rpx;
            }

            .text {
                font-size: 38rpx;
                padding-top: 20rpx;
            }
        }
    }
    .content {
        padding: 30rpx;
        .row {
            border-bottom: 1px dotted #cfcfcf;
            padding: 20rpx 0;
        }
    }
    .nohistory {
        display: flex;
        justify-content: center;
        flex-direction: column;
        align-items: center;
        image {
            width: 400rpx;
        }
        .text {
            font-style: 26rpx;
            color: #888;
        }
    }
</style>
