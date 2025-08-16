<template>
  <view class="evaluation-page">
    <view class="page-title">
      <text></text>

      <view class="back" @click="$toBack">
        <uni-icons class="back" color="#1A1A1A" type="left" size="22"></uni-icons>
      </view>
    </view>

    <view class="banner"> </view>

    <view class="evaluation-container">
      <view class="evaluation-box">
        <view class="evaluation-title">
          <text>{{ evaluationList[stepIndex].title }}</text>
          <text>{{ evaluationList[stepIndex].subTitle }}</text>
        </view>

        <view class="evaluation evaluation1" v-if="stepIndex === 0">
          <view class="gender">
            <view class="gender-item" :class="{ active: gender === 1 }" @click="gender = 1">
              <image
                mode="widthFix"
                src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app/evaluation/gender1.png"
              />
              <text>男生</text>
            </view>

            <view class="gender-item" :class="{ active: gender === 2 }" @click="gender = 2">
              <image
                mode="widthFix"
                src="https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app/evaluation/gender2.png"
              />
              <text>女生</text>
            </view>
          </view>
        </view>

        <view class="evaluation evaluation2" v-if="stepIndex === 1">
          <picker-view
            indicator-style="height: 40px;"
            style="width: 100%; height: 200px"
            :value="birth"
            @change="birth = $event.detail.value"
          >
            <picker-view-column>
              <view class="age-item" v-for="(item, index) in years" :key="index">
                <text>{{ item }}</text>
                <text>年</text>
              </view>
            </picker-view-column>

            <picker-view-column>
              <view class="age-item" v-for="(item, index) in months" :key="index">
                <text>{{ item }}</text>
                <text>月</text>
              </view>
            </picker-view-column>
          </picker-view>
        </view>

        <view class="evaluation evaluation3" v-if="stepIndex === 2">
          <picker-view
            indicator-style="height: 40px;"
            style="width: 100%; height: 200px"
            :value="height"
            @change="height = $event.detail.value"
          >
            <picker-view-column>
              <view class="age-item" v-for="(item, index) in rulerLineList1" :key="index">
                <text>{{ item }}</text>
                <text>CM</text>
              </view>
            </picker-view-column>
          </picker-view>
        </view>

        <view class="evaluation evaluation4" v-if="stepIndex === 3">
          <picker-view
            indicator-style="height: 40px;"
            style="width: 100%; height: 200px"
            :value="initialWeight"
            @change="initialWeight = $event.detail.value"
          >
            <picker-view-column>
              <view class="age-item" v-for="(item, index) in rulerLineList2" :key="index">
                <text>{{ item }}</text>
                <text>KG</text>
              </view>
            </picker-view-column>
          </picker-view>
        </view>

        <view class="evaluation evaluation5" v-if="stepIndex === 4">
          <picker-view
            indicator-style="height: 40px;"
            style="width: 100%; height: 200px"
            :value="targetWeight"
            @change="targetWeight = $event.detail.value"
          >
            <picker-view-column>
              <view class="age-item" v-for="(item, index) in rulerLineList3" :key="index">
                <text>{{ item }}</text>
                <text>KG</text>
              </view>
            </picker-view-column>
          </picker-view>
        </view>

        <!-- TODO -->
        <view class="evaluation evaluation6" v-if="stepIndex === 5"></view>

        <view class="evaluation evaluation7" v-if="stepIndex === 6">
          <picker-view
            indicator-style="height: 40px;"
            style="width: 100%; height: 200px"
            :value="exerciseHabitsIndex"
            @change="exerciseHabitsIndex = $event.detail.value"
          >
            <picker-view-column>
              <view class="age-item" v-for="(item, index) in exerciseHabits.map((item) => item.text)" :key="index">
                <text>{{ item }}</text>
              </view>
            </picker-view-column>
          </picker-view>
        </view>

        <view class="evaluation-title2">
          <text>{{ evaluationList[stepIndex].subTitle2 }}</text>
        </view>

        <view class="dot">
          <text :class="{ active: stepIndex === 0 }"></text>
          <text :class="{ active: stepIndex === 1 }"></text>
          <text :class="{ active: stepIndex === 2 }"></text>
          <text :class="{ active: stepIndex === 3 }"></text>
          <text :class="{ active: stepIndex === 4 }"></text>
          <text :class="{ active: stepIndex === 5 }"></text>
          <text :class="{ active: stepIndex === 6 }"></text>
        </view>

        <view class="next" @click="next">{{ stepIndex > 5 ? '提交' : '下一步' }}</view>
      </view>
    </view>
  </view>
</template>

<script>
import $http from '@/utils/http';

export default {
  name: 'evaluation',

  data() {
    const years = [];
    const months = [];

    for (let i = 1925; i < 2025; i++) {
      years.push(i);
    }

    for (let i = 1; i <= 12; i++) {
      months.push(i < 10 ? '0' + i : i);
    }

    let rulerLineList1 = [];
    let rulerLineList2 = [];
    let rulerLineList3 = [];

    for (let i = 0; i < 305; i++) {
      rulerLineList1.push(i);
    }

    for (let i = 0; i < 305; i += 0.5) {
      rulerLineList2.push(i);
      rulerLineList3.push(i);
    }

    return {
      evaluationList: [
        {
          title: '你的性别',
          subTitle: '完成数据，为你提供个性化专属方案\n选择后不可修改',
          subTitle2: '选择你的性别，用来准确计算你的BMI值',
        },
        {
          title: '你的出生年月',
          subTitle: '完成数据，为你提供个性化专属方案\n选择后不可修改',
          subTitle2: '选择你的年龄，用来准确计算你的BMI值',
        },
        {
          title: '你的身高',
          subTitle: '完成数据，为你提供个性化专属方案\n选择后不可修改',
          subTitle2: '选择你的身高，用来准确计算你的BMI值',
        },
        {
          title: '你的体重',
          subTitle: '完成数据，为你提供个性化专属方案',
          subTitle2: '选择你的体重，用来准确计算你的BMI值',
        },
        {
          title: '你的目标体重',
          subTitle: '完成数据，为你提供个性化专属方案',
          subTitle2: '选择你的目标体重，用来估算你的减重周期',
        },
        {
          title: '你的目标日期',
          subTitle: '完成数据，为你提供个性化专属方案',
          subTitle2: '选择你的目标日期，用来估算你的减重量',
        },
        {
          title: '你的运动量',
          subTitle: '完成数据，为你提供个性化专属方案',
          subTitle2: '选择你的运动量，为你提供相关的运动建议',
        },
      ],
      stepIndex: 0,
      gender: null,
      years,
      months,
      birth: [80, 0],
      rulerLineList1: rulerLineList1,
      height: [170],
      rulerLineList2: rulerLineList2,
      initialWeight: [140],
      rulerLineList3: rulerLineList3,
      targetWeight: [120],
      endYears: [],
      endMonths: [],
      endDays: [],
      end_date: [],
      exerciseHabits: [
        {
          id: 0,
          value: 1,
          text: '几乎不运动',
          active: true,
        },
        {
          id: 1,
          value: 2,
          text: '偶尔运动，每周1-2天',
          active: false,
        },
        {
          id: 2,
          value: 3,
          text: '经常运动，每周3-5天',
          active: false,
        },
        {
          id: 3,
          value: 4,
          text: '运动频繁，每周6-7天',
          active: false,
        },
        {
          id: 4,
          value: 5,
          text: '高强度运动，长时间体力运动',
          active: false,
        },
      ],
      exerciseHabitsIndex: 0,
      planData: null,
    };
  },

  onShareAppMessage() {
    return {
      title: 'AI饮食记录小程序',
      imageUrl: 'https://hnenjoy.oss-cn-shanghai.aliyuncs.com/food-diary-app/share-img.jpg',
      path: '/pages/index/index',
    };
  },

  methods: {
    next() {
      if (this.stepIndex === 0) {
        if (!this.gender) {
          uni.showToast({
            title: '请选择性别',
            icon: 'none',
          });

          return;
        }
      }

      if (this.stepIndex === 4) {
        setTimeout(() => {
          //  TODO
          // let weight = Math.abs(this.initialWeight[0] / 2 - this.targetWeight[0] / 2);
          // let time = Date.now() + 7 * 24 * 60 * 60 * 1000 * Math.ceil(weight / 0.5);
          //
          // let currentDate = new Date(time);
          //
          // const year = currentDate.getFullYear();
          // const month = currentDate.getMonth() + 1 > 9 ? currentDate.getMonth() + 1 : `0${currentDate.getMonth() + 1}`;
          // const date = currentDate.getDate() > 9 ? currentDate.getDate() : `0${currentDate.getDate()}`;
          //
          // let startTime = new Date().format().slice(0, 10);
          // let endTime = `${year}/${month}/${date}`;
          //
          // let timePicker = '';
        }, 0);
      }

      if (this.stepIndex === 6) {
        uni.showLoading({
          title: '加载中...',
          mask: true,
        });

        let birth_year = this.years[this.birth[0]].toString() + '/' + this.months[this.birth[1]].toString();

        $http
          .post('api/diet-info/user-info/update', {
            gender: this.gender,
            birth_year: new Date(birth_year).format(),
            height: this.height[0],
            initial_weight: this.initialWeight[0] / 2,
            target_weight: this.targetWeight[0] / 2,
            exercise_habits: this.exerciseHabits[this.exerciseHabitsIndex].value,
            begin_date: new Date().format(),
            end_date: new Date(this.$refs.calendarRef.selectedDate).format(),
          })
          .then(() => {
            uni.hideLoading();

            uni.showToast({
              title: '提交成功',
              icon: 'none',
              mask: true,
            });

            setTimeout(() => {
              this.$toBack();
            }, 1000);
          });

        return;
      }

      this.stepIndex += 1;
    },
  },
};
</script>

<style lang="scss">
page {
  height: 100%;
}
</style>

<style scoped lang="scss">
.evaluation-page {
  height: 100%;
  padding-bottom: 80rpx;
  display: flex;
  flex-direction: column;

  .page-title {
    flex-shrink: 0;
  }

  .banner {
    padding: calc(var(--page-title-height)) 0 0;
    flex-shrink: 0;
  }

  .evaluation-container {
    padding: 130rpx 100rpx 0;
    flex-grow: 1;
    overflow: hidden;

    .evaluation-box {
      height: 100%;
      background: #ffffff;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;

      .evaluation-title {
        flex-shrink: 0;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        gap: 30rpx;

        text {
          &:nth-child(1) {
            font-weight: bold;
            font-size: 36rpx;
            color: #111111;
          }

          &:nth-child(2) {
            font-size: 28rpx;
            color: #999999;
            line-height: 61rpx;
            text-align: center;
          }
        }
      }

      .evaluation {
        flex-grow: 1;
      }

      .evaluation1 {
        margin-top: 150rpx;

        .gender {
          padding: 0 15rpx;
          display: flex;
          align-items: center;
          justify-content: center;
          gap: 110rpx;

          .gender-item {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            gap: 56rpx;
            position: relative;

            &.active {
              image {
              }

              text {
                background: #5664e5;
                border: 1px solid #5664e5;
                color: #ffffff;
              }
            }

            image {
              width: 198rpx;
              border-radius: 50%;
            }

            text {
              width: 140rpx;
              height: 56rpx;
              background: #ffffff;
              border-radius: 28rpx;
              border: 1px solid #e0e0e0;
              font-weight: 500;
              font-size: 28rpx;
              color: #111111;
              display: flex;
              align-items: center;
              justify-content: center;
            }
          }
        }
      }

      .evaluation2,
      .evaluation3,
      .evaluation4,
      .evaluation5,
      .evaluation7 {
        width: 100%;
        margin-top: 120rpx;

        &.evaluation7 {
          .age-item {
            text {
              &:nth-child(1) {
                font-size: 32rpx;
                color: #111111;
                margin-right: 20rpx;
              }
            }
          }
        }

        picker-view {
          .age-item {
            display: flex;
            align-items: center;
            justify-content: center;

            text {
              &:nth-child(1) {
                font-size: 40rpx;
                color: #111111;
                margin-right: 20rpx;
              }

              &:nth-child(2) {
                font-size: 24rpx;
                position: relative;
                top: 4rpx;
              }
            }
          }
        }
      }

      .evaluation6 {
        width: 100%;
        margin-top: 120rpx;
        display: flex;
        flex-direction: column;
        justify-content: space-around;
      }

      .evaluation-title2 {
        font-size: 24rpx;
        color: #999999;
        margin-bottom: 100rpx;
      }

      .dot {
        margin-bottom: 38rpx;
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 41rpx;

        text {
          width: 15rpx;
          height: 15rpx;
          background: #d9d9d9;
          border-radius: 50%;

          &.active {
            background: #5664e5;
          }
        }
      }

      .next {
        flex-shrink: 0;
        width: 550rpx;
        height: 100rpx;
        background: linear-gradient(90deg, #4f69e6 0%, #6b56e3 100%);
        border-radius: 50rpx;
        font-weight: bold;
        font-size: 30rpx;
        color: #ffffff;
        display: flex;
        align-items: center;
        justify-content: center;
      }
    }
  }
}
</style>
