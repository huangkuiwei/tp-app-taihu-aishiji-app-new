<template>
  <view class="my-page">
    <view class="page-title">个人中心</view>

    <view class="banner"></view>

    <view class="page-container">
      <view class="info">
        <view class="left">
          <image
            mode="aspectFill"
            :src="
              userInfo.avatar_url || 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app/my/default_head.png'
            "
          />
        </view>

        <view class="right">
          <view class="username" v-if="isLogin">
            <text class="name">{{ userInfo.uname }}</text>
          </view>

          <view class="no-login" v-else @click="$toRouter('/packageLogin/pages/login/login')">
            <text class="name">登录/注册</text>
            <text class="tip">游客模式</text>
          </view>

          <template v-if="isLogin">
            <view class="time" v-if="isVip">
              <text>会员到期：{{ userInfo.vip_info.vip_end_time.slice(0, 10) }}</text>
              <text class="renewal" @click="$toRouter('/pages/renewalManage/renewalManage')">续订管理</text>
            </view>
            <view class="time" v-else>普通用户</view>
          </template>
        </view>

        <view class="setting">
          <image
            mode="widthFix"
            @click="goProfile"
            src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/my/setting.png"
          />
        </view>
      </view>

      <view class="vip-icon" @click="!isVip && $toRouter('/pages/vip/vip')">
        <!-- TODO VIP 图片更换 -->
        <image
          class="bg"
          mode="widthFix"
          :src="
            isVip
              ? 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/my/vip-icon.png'
              : 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/my/vip-icon.png'
          "
        />

        <image
          class="btn"
          mode="widthFix"
          :src="
            isVip
              ? 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/my/btn1.png'
              : 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/my/btn1.png'
          "
        />
      </view>

      <view class="data">
        <view class="title">
          <text>数据报告</text>
          <text v-if="userDetailInfo" @click="previewDataPage('/pages/dataReport/dataReport')">查看更多</text>
        </view>

        <view class="data-box" v-if="userDetailInfo">
          <view class="left">
            <view class="left-title">
              {{ isWeightLoss ? '本次减重（公斤）' : '本次增肌（公斤）' }}
            </view>
            <view class="chart">
              <l-echart ref="chart1Ref" @finished="init1" />

              <view class="chart-data">
                <view>{{ lossWeightData.a }}</view>
                <view>
                  <text v-if="lossWeightData.b > 0">还差{{ lossWeightData.b }}</text>
                  <text v-else>已完成</text>
                </view>
              </view>
            </view>
            <view class="data-detail">
              <view class="item1">
                <text>{{ userDetailInfo.initial_weight }}</text>
                <text>初始</text>
              </view>

              <view class="item2">
                <image
                  mode="widthFix"
                  src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/my/icon1.png"
                />
              </view>

              <view class="item3">
                <text>{{ userDetailInfo.target_weight }}</text>
                <text>目标</text>
              </view>
            </view>
          </view>

          <view class="right">
            <view class="right-data right-data1">
              <view class="data-title">BMI</view>
              <view class="data-value">
                {{
                  Number(
                    (userDetailInfo.current_weight / ((userDetailInfo.height * userDetailInfo.height) / 10000)).toFixed(
                      1,
                    ),
                  )
                }}
              </view>
            </view>

            <!-- TODO 整体目标 剩余天数？ -->
            <view class="right-data right-data2">
              <view class="data-title">剩余天数</view>
              <view class="data-value">23</view>
            </view>
          </view>
        </view>

        <view class="add-info-tip" v-else>
          <view>
            请先
            <text class="highlight" v-if="isLogin" @click="$toRouter('/pages/evaluation/evaluation')">
              完善个人基本信息
            </text>
            <text class="highlight" v-else @click="$toRouter('/packageLogin/pages/login/login')">登录</text>
          </view>
        </view>
      </view>

      <view class="nav-container">
        <view class="nav">
          <view class="nav-item" @click="goUserCenter">
            <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/my/menu1.png" />
            <text class="nav-title">我的数据</text>
            <uni-icons color="#999999" type="right" size="14"></uni-icons>
          </view>

          <view class="nav-item" @click="jumpAuthPage('/pages/setReminder/setReminder')">
            <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/my/menu2.png" />
            <text class="nav-title">提醒设置</text>
            <uni-icons color="#999999" type="right" size="14"></uni-icons>
          </view>

          <view class="nav-item" @click="jumpAuthPage('/pages/redemption/redemption')">
            <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/my/menu3.png" />
            <text class="nav-title">兑换码</text>
            <uni-icons color="#999999" type="right" size="14"></uni-icons>
          </view>

          <view class="nav-item" @click="jumpAuthPage('/pages/feedback/feedback')">
            <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/my/menu4.png" />
            <text class="nav-title">意见和反馈</text>
            <uni-icons color="#999999" type="right" size="14"></uni-icons>
          </view>

          <view class="nav-item">
            <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/my/menu5.png" />
            <button class="nav-title" @click="openContact">联系客服</button>
            <uni-icons color="#999999" type="right" size="14"></uni-icons>
          </view>

          <view class="nav-item" @click="jumpAuthPage('/pages/about/about')">
            <image mode="widthFix" src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/my/menu6.png" />
            <text class="nav-title">关于我们</text>
            <uni-icons color="#999999" type="right" size="14"></uni-icons>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
import { mapActions, mapGetters, mapState } from 'vuex';
import * as echarts from '@/uni_modules/lime-echart/static/echarts.min';
import { verifyIsLogin } from '@/utils';

let chart1 = null;

export default {
  name: 'myPage',

  data() {
    return {
      option1: {
        series: [
          {
            type: 'gauge',
            startAngle: 90,
            endAngle: -270,
            min: 0,
            max: 100,
            splitNumber: 12,
            itemStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                {
                  offset: 0,
                  color: '#5168E6', // 起始颜色
                },
                {
                  offset: 1,
                  color: '#6B57E3', // 结束颜色
                },
              ]),
              shadowColor: 'transparent',
              shadowBlur: 10,
              shadowOffsetX: 2,
              shadowOffsetY: 2,
            },
            progress: {
              show: true,
              roundCap: true,
              width: 4,
            },
            pointer: {
              show: false,
            },
            axisLine: {
              roundCap: true,
              lineStyle: {
                width: 4,
                color: [[1, '#DBDCEC']],
              },
            },
            radius: '100%',
            axisTick: {
              show: false,
            },
            splitLine: {
              show: false,
            },
            axisLabel: {
              show: false,
            },
            title: {
              show: false,
            },
            detail: {
              show: false,
            },
            data: [
              {
                value: 0,
              },
            ],
          },
        ],
      },
    };
  },

  computed: {
    ...mapState('app', ['userInfo', 'userDetailInfo']),
    ...mapGetters('app', ['isLogin', 'isVip']),

    isWeightLoss() {
      if (!this.isLogin || !this.userDetailInfo) {
        return true;
      }

      return this.userDetailInfo.initial_weight - this.userDetailInfo.target_weight > 0;
    },

    lossWeightData() {
      if (!this.userDetailInfo) {
        return {};
      }

      if (this.isWeightLoss) {
        return {
          a: Number((this.userDetailInfo.initial_weight - this.userDetailInfo.current_weight).toFixed(2)),
          b: Number((this.userDetailInfo.current_weight - this.userDetailInfo.target_weight).toFixed(2)),
        };
      } else {
        return {
          a: Number((this.userDetailInfo.current_weight - this.userDetailInfo.initial_weight).toFixed(2)),
          b: Number((this.userDetailInfo.target_weight - this.userDetailInfo.current_weight).toFixed(2)),
        };
      }
    },
  },

  onShow() {
    this._getUserInfo();
    this._getUserDetailInfo();
  },

  onShareAppMessage() {
    return {
      title: 'AI饮食记录小程序',
      imageUrl: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app/share-img.jpg',
      path: '/pages/index/index',
    };
  },

  methods: {
    ...mapActions('app', ['_getUserDetailInfo', '_getUserInfo']),

    async init1() {
      chart1 = await this.$refs.chart1Ref.init(echarts);
      chart1.setOption(this.option1);
    },

    previewDataPage(url) {
      verifyIsLogin();

      if (!this.userDetailInfo) {
        this.$toRouter('/pages/evaluation/evaluation');
        return;
      }

      this.$toRouter(url);
    },

    // TODO 客服修改
    openContact() {
      uni.openCustomerServiceChat({
        corpId: 'wwa09afa94a53c191b',
        extInfo: {
          url: 'https://work.weixin.qq.com/kfid/kfc4d6f5ec87ef53883',
        },
      });
    },

    jumpAuthPage(url) {
      verifyIsLogin();
      this.$toRouter(url);
    },

    goProfile() {
      verifyIsLogin();
      this.$toRouter('/pages/profile/profile');
    },

    goUserCenter() {
      verifyIsLogin();

      if (!this.userDetailInfo) {
        this.$toRouter('/pages/evaluation/evaluation');
        return;
      }

      this.$toRouter('/pages/userCenter/userCenter');
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
.my-page {
  padding-bottom: 60rpx;

  .page-title {
  }

  .banner {
    padding: calc(var(--page-title-height) + 23rpx) 0 0;
  }

  .page-container {
    padding: 0 30rpx;

    .info {
      display: flex;
      align-items: center;
      margin-bottom: 33rpx;

      .left {
        margin-right: 18rpx;

        image {
          width: 130rpx;
          height: 130rpx;
          border-radius: 50%;
          display: block;
        }
      }

      .right {
        display: flex;
        flex-direction: column;
        gap: 21rpx;
        flex-grow: 1;

        .username {
          display: flex;
          align-items: center;

          .name {
            font-weight: bold;
            font-size: 26rpx;
            color: #222222;
          }
        }

        .no-login {
          display: flex;
          flex-direction: column;
          gap: 21rpx;

          .name {
            font-weight: bold;
            font-size: 26rpx;
            color: #222222;
          }

          .tip {
            font-weight: 500;
            font-size: 22rpx;
            color: #333333;
          }
        }

        .time {
          font-weight: 500;
          font-size: 22rpx;
          color: #333333;
          display: flex;
          align-items: center;
          gap: 20rpx;

          .renewal {
            width: 105rpx;
            height: 35rpx;
            background: #0abf92;
            border-radius: 18rpx;
            font-size: 20rpx;
            color: #ffffff;
            display: flex;
            align-items: center;
            justify-content: center;
          }
        }
      }

      .setting {
        image {
          width: 41rpx;
        }
      }
    }

    .vip-icon {
      width: 100%;
      margin-bottom: 23rpx;
      position: relative;

      .bg {
        width: 100%;
      }

      .btn {
        width: 160rpx;
        position: absolute;
        top: 33rpx;
        right: 43rpx;
      }
    }

    .data {
      background: #ffffff;
      box-shadow: 0 3rpx 21rpx 0 rgba(215, 218, 242, 0.29);
      border-radius: 20rpx;
      padding: 30rpx;
      margin-bottom: 21rpx;

      .title {
        display: flex;
        align-items: center;
        justify-content: space-between;
        margin-bottom: 30rpx;

        text {
          &:nth-child(1) {
            color: #111111;
            font-size: 30rpx;
            font-weight: 500;
          }

          &:nth-child(2) {
            color: #999999;
            font-size: 24rpx;
          }
        }
      }

      .data-box {
        display: flex;
        align-items: center;
        justify-content: space-between;

        .left,
        .right {
          width: calc(50% - 5rpx);

          &.left {
            background: #f6f7fb;
            border-radius: 14rpx;
            padding: 22rpx;

            .left-title {
              font-weight: 500;
              font-size: 26rpx;
              color: #222222;
              margin-bottom: 40rpx;
            }

            .chart {
              width: 200rpx;
              height: 200rpx;
              margin: 0 auto 23rpx;
              position: relative;

              .chart-data {
                position: absolute;
                left: 0;
                right: 0;
                top: 0;
                bottom: 0;
                display: flex;
                flex-direction: column;
                align-items: center;
                justify-content: center;
                gap: 19rpx;

                view {
                  &:nth-child(1) {
                    font-weight: 500;
                    font-size: 40rpx;
                    color: #222222;
                  }

                  &:nth-child(2) {
                    background: #ecedff;
                    border-radius: 19rpx;
                    font-size: 24rpx;
                    color: #5664e5;
                    padding: 6rpx 16rpx;
                  }
                }
              }
            }

            .data-detail {
              display: flex;
              align-items: center;
              justify-content: space-between;

              .item1,
              .item3 {
                display: flex;
                flex-direction: column;
                align-items: center;
                justify-content: center;
                gap: 10rpx;

                text {
                  &:nth-child(1) {
                    color: #333333;
                    font-size: 32rpx;
                  }

                  &:nth-child(2) {
                    color: #666666;
                    font-size: 26rpx;
                  }
                }
              }

              .item2 {
                image {
                  width: 19rpx;
                }
              }
            }
          }

          &.right {
            display: flex;
            flex-direction: column;
            justify-content: space-between;

            .right-data {
              padding: 20rpx 31rpx 67rpx;

              &.right-data1 {
                background: url('https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/my/bg1.png') left top/100%
                  100% no-repeat;
              }

              &.right-data2 {
                background: url('https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app2/my/bg2.png') left top/100%
                  100% no-repeat;
                position: relative;
                top: 10rpx;
              }

              .data-title {
                font-weight: 500;
                font-size: 26rpx;
                color: #222222;
                margin-bottom: 56rpx;
              }

              .data-value {
                font-weight: 500;
                font-size: 34rpx;
                color: #222222;
              }
            }
          }
        }
      }

      .add-info-tip {
        font-size: 26rpx;
        text-align: center;
        padding: 40rpx 0 20rpx;

        .highlight {
          color: #5664e5;
        }
      }
    }

    .nav-container {
      .nav {
        background: #ffffff;
        border-radius: 20rpx;
        padding: 40rpx 30rpx;
        display: flex;
        flex-direction: column;

        .nav-item {
          display: flex;
          align-items: center;

          &:not(:last-child) {
            padding-bottom: 30rpx;
            margin-bottom: 30rpx;
            border-bottom: 2rpx solid #f6f6f6;
          }

          image {
            width: 60rpx;
            margin-right: 20rpx;
          }

          .nav-title {
            font-weight: 500;
            font-size: 28rpx;
            color: #111111;
            flex-grow: 1;
          }

          button {
            text-align: left;
            background: transparent;
            padding: 0 0 0 2rpx;
            margin: 0;

            &:after {
              border: none;
            }
          }
        }
      }
    }
  }
}
</style>
