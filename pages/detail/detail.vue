<template>
    <view class="detail">
        <view class="title">
            {{detail.title}}
        </view>
        <view class="info">
            <view class="author">编辑：{{detail.author}}</view>
            <view class="author">发布日期：{{detail.posttime}}</view>
        </view>
        <view class="content">
            <rich-text :nodes="detail.content"></rich-text>
        </view>
        <view class="des">
            声明：版本的表单绑定版本的表单绑定版本的表单绑定版本的表单绑定版本的表单绑定版本的表单绑定版本的表单绑定
        </view>
    </view>
</template>

<script>
    import {parseTime} from '@/utils/tool.js'
    export default {
        data() {
            return {
                options: null,
                detail: {}
            };
        },
        onLoad(e) {
            console.log(e);
            this.options = e
            this.getDetail()
        },
        methods: {
            getDetail() {
                uni.request({
                    url: 'https://ku.qingnian8.com/dataApi/news/detail.php',
                    data: this.options,
                    success: res => {
                        console.log(res);
                        res.data.posttime = parseTime(res.data.posttime)
                        res.data.content = res.data.content.replace(/<img/gi, '<img style="max-width: 100%"')
                        this.detail = res.data
                        
                        this.saveHistory()
                        
                        uni.setNavigationBarTitle({
                            title: this.detail.title
                        })
                    }
                })
            },
            saveHistory() {
                let historyArr = uni.getStorageSync("historyArr") || []
                let item = {
                    id: this.detail.id,
                    classid: this.detail.classid,
                    title: this.detail.title,
                    picurl: this.detail.picurl,
                    looktime: parseTime(Date.now())
                }
                
                let index = historyArr.findIndex(i => {
                    return i.id == this.detail.id
                })
                
                if(index >= 0) {
                    historyArr.splice(index, 1)
                }
                
                historyArr.unshift(item)
                historyArr = historyArr.splice(0, 10)
                uni.setStorageSync("historyArr", historyArr)
            }
        }
    }
</script>

<style lang="scss">
.detail {
    padding: 30rpx;
    .title {
        font-size: 46rpx;
        color: #333;
    }
    .info {
        padding: 20rpx 0;
        font-size: 26rpx;
        background: #f6f6ff;
        color: #666;
        display: flex;
        justify-content: space-between;
        margin: 40rpx 0;
    }
    .content {
        padding-bottom: 50rpx;
        /deep/ img {
            max-width: 100%;
        }
    }
    .des {
        font-size: 26rpx;
        padding: 20rpx;
        color: #f89898;
        line-height: 1.8rem;
        background: #fef0f0;
    }
}
</style>
