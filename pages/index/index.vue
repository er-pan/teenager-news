<template>
	<view class="home">
        <view class="scrollNav">
            <scroll-view scroll-x class="navscroll">
                <view @click="clickNav(index, item.id)" 
                :class="index == navIndex ? 'active' : ''" class="item" 
                v-for="(item, index) in navArr" :key="item.id">
                    {{item.classname}}
                </view>
            </scroll-view>
        </view>
        <view class="content">
            <view class="row" v-for="item in newsArr" :key="item.id">
                <newsbox :item="item" @click.native="gotoDetail(item)"></newsbox>
            </view>
        </view>
        <view class="nodata" v-if="!newsArr.length">
            <image src="../../static/nodata.png" mode="widthFix"></image>
        </view>
        
        <view class="loading" v-if="newsArr.length">
            <view v-if="loading == 1">数据加载中...</view>
            <view v-if="loading == 2">我也是有底线的..</view>
        </view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				navIndex: 0,
                navArr: [],
                newsArr: [],
                currentpage: 1,
                loading: 0
			}
		},
		onLoad() {
            this.getNavData()
            this.getNewsData()
		},
        onReachBottom() {
            console.log('到底了');
            if(this.loading == 2) return
            this.currentpage++
            this.loading = 1
            this.getNewsData()
        },
		methods: {
            //点击切换导航
            clickNav(index, id) {
                // console.log(id);
                this.navIndex = index
                this.currentpage = 1
                this.loading = 0
                this.newsArr = []
                this.getNewsData(id)
            },
            //跳转到详情页
            gotoDetail(item) {
                uni.navigateTo({
                    url: `/pages/detail/detail?cid=${item.classid}&id=${item.id}`,
                })
            },
            //获取导航列表数据
            getNavData() {
                uni.request({
                    url: 'https://ku.qingnian8.com/dataApi/news/navlist.php',
                    success: res => {
                        // console.log(res);
                        this.navArr = res.data
                    }
                })
            },
            //获取新闻列表数据
            getNewsData(id = 50) {
                uni.request({
                    url: 'https://ku.qingnian8.com/dataApi/news/newslist.php',
                    data: {
                        cid: id,
                        page: this.currentpage++
                        
                    },
                    success: res => {
                        console.log(res);
                        if(res.data.length == 0) {
                            this.loading = 2
                        }
                        this.newsArr = [...this.newsArr, ...res.data]
                    }
                })
            }
		}
	}
</script>

<style lang="scss" scoped>
.navscroll {
    height: 100rpx;
    background: #f7f8fa;
    white-space: nowrap;
    position: fixed;
    top: var(--window-top);
    left: 0;
    z-index: 10;
    /deep/ ::-webkit-scrollbar {
        width: 4px !important;
        height: 1px !important;
        overflow: auto !important;
        background: transparent !important;
        -webkit-appearance: auto !important;
        display: block;
    }
    .item {
        font-size: 40rpx;
        display: inline-block;
        line-height: 100rpx;
        padding: 0 30rpx;
        &.active {
            color: #31C27C;
        }
    }
}

.content {
    padding: 30rpx;
    padding-top: 130rpx;
    .row {
        border-bottom: 1px dotted #cfcfcf;
        padding: 20rpx 0;
    }
}
.nodata {
    display: flex;
    justify-content: center;
    image {
        width: 360rpx;
    }
}
.loading {
    text-align: center;
    font-size: 26rpx;
    color: #888;
    line-height: 2rem;
}
</style>
