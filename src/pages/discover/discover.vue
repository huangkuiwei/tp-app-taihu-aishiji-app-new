<template>
  <view class="discover-page">
    <view class="page-title">发现</view>
    <view class="banner"></view>

    <view class="card-container">
      <view class="question-card">
        <view class="card-title">
          <view class="word">猜你想问：</view>
          <image
            mode="widthFix"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/discover/icon1.png"
          />
        </view>

        <view class="question-list">
          <!-- TODO 问题列表 -->
          <view class="question-detail"></view>
          <view class="ask-btn" @click="jumpAi(jkzsChat)">
            <image
              mode="widthFix"
              src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/discover/ask-btn.png"
            />
          </view>
        </view>
      </view>

      <view class="ai-container">
        <view class="ar-title">智能工具</view>

        <view class="ai-list">
          <view class="ai-item" v-for="(item, index) of aiChartList" :key="index" @click="jumpAi(item)">
            <view class="top">
              <text>{{ item.name }}</text>

              <image
                mode="widthFix"
                src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/discover/icon2.png"
              />
            </view>

            <view class="bottom">
              <text>以少油少糖多\n纤维为核心原则</text>
              <image mode="widthFix" :src="item.logo" />
            </view>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
import { mapGetters, mapState } from 'vuex';
import $http from '@/utils/http';

export default {
  name: 'discoverPage',

  data() {
    return {
      aiChartList: [],
      jkzsChat: {},
    };
  },

  onShow() {
    this.initData();
  },

  computed: {
    ...mapState('app', ['userDetailInfo']),
    ...mapGetters('app', ['isLogin']),
  },

  onShareAppMessage() {
    return {
      title: 'AI饮食记录小程序',
      imageUrl: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app/share-img.jpg',
      path: '/pages/index/index',
    };
  },

  methods: {
    /**
     * 初始化数据
     */
    async initData() {
      uni.showLoading({
        title: '加载中...',
        mask: true,
      });

      await this.getAiChartList();
      uni.hideLoading();
    },

    /**
     * 获取 ai 搭子列表
     */
    getAiChartList() {
      $http
        .get('api/baseai/agent-list')
        .then((res) => {
          let jkzsChatIndex = res.data.findIndex((item) => item.id === 10000);

          if (jkzsChatIndex !== -1) {
            this.jkzsChat = res.data[jkzsChatIndex];
          }

          res.data.splice(jkzsChatIndex, 1);
          this.aiChartList = res.data;
        })
        .catch(() => {
          this.aiChartList = [];
        });
    },

    /**
     * 跳转 AI 搭子聊天界面
     */
    jumpAi(item) {
      // verifyIsLogin();

      if (item.id === 10000) {
        this.$toRouter('/pages/healthAssistant/healthAssistant', `agent_id=${item.id}&name=${item.name}`);
        return;
      }

      this.$toRouter('/pages/aiChat/aiChat', `agent_id=${item.id}&name=${item.name}`);
    },
  },
};
</script>

<style>
page {
  background: #f6f7fb url('https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/home/banner-bg.png') left
    top/100% auto no-repeat;
}
</style>

<style scoped lang="scss">
.discover-page {
  .page-title {
  }

  .banner {
    padding: calc(var(--page-title-height) + 39rpx) 0 0;
  }

  .card-container {
    padding: 0 30rpx 26rpx;
    position: relative;

    .question-card {
      margin-bottom: 30rpx;

      .card-title {
        padding: 41rpx 29rpx 55rpx;
        background: #5664e5;
        border-top-left-radius: 20rpx;
        border-top-right-radius: 20rpx;

        .word {
          font-weight: 500;
          font-size: 32rpx;
          color: #ffffff;
        }

        image {
          position: absolute;
          width: 172rpx;
          right: 27rpx;
          top: -30rpx;
        }
      }

      .question-list {
        height: 480rpx;
        background: #ffffff;
        box-shadow: 0 3rpx 21rpx 0 rgba(215, 218, 242, 0.29);
        border-radius: 20rpx;
        position: relative;
        top: -14rpx;

        .question-detail {
          height: 320rpx;
          margin-bottom: 29rpx;
        }

        .ask-btn {
          display: flex;
          align-items: center;
          justify-content: center;

          image {
            width: 287rpx;
          }
        }
      }
    }

    .ai-container {
      .ar-title {
        font-weight: 500;
        font-size: 30rpx;
        color: #111111;
        margin-bottom: 30rpx;
      }

      .ai-list {
        display: flex;
        flex-wrap: wrap;
        justify-content: space-between;
        gap: 20rpx;

        .ai-item {
          width: calc(50% - 12rpx);
          background: #ffffff;
          border-radius: 15rpx;
          padding: 30rpx 21rpx;

          .top {
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin-bottom: 20rpx;

            text {
              font-weight: 500;
              font-size: 28rpx;
              color: #111111;
            }

            image {
              width: 12rpx;
            }
          }

          .bottom {
            display: flex;
            align-items: center;
            justify-content: space-between;

            text {
              font-size: 22rpx;
              color: #999999;
              white-space: pre-wrap;
              line-height: 30rpx;
            }

            image {
              width: 55rpx;
            }
          }
        }
      }
    }
  }
}
</style>
