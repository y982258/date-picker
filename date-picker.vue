<template>
  <div v-click-outside>
    <input type="text" :value="formatDate" />
    <div class="pannel" v-if="isVisible">
      <div class="pannel-nav">
        <span @click="prevYear">&lt;</span>
        <span @click="prevMonth">&lt;&lt;</span>
        <span>{{tiem.year}}年</span>
        <span>{{tiem.month+1}}月</span>
        <span @click="nextMonth">&gt;&gt;</span>
        <span @click="nextYear">&gt;</span>
      </div>
      <div class="pannel-content">
        <div class="days">
          <span v-for="j in 7" class="ceel" :key="`_`+j">{{weekDays[j-1]}}</span>
          <!-- 直接列表出一个 6*7 列表 -->
          <div v-for="i in 6" :key="i">
            <span
              class="ceel ceel_day"
              :class="[
            {
                notCurrentMonth:!isCurrentMonth(visibeDays[(i-1)*7+(j-1)]),
                
            },{
                today:istoday(visibeDays[(i-1)*7+(j-1)])
            },{
                select:isSelect(visibeDays[(i-1)*7+(j-1)])
            }]"
              v-for="j in 7"
              :key="j"
              @click="chooseDate(visibeDays[(i-1)*7+(j-1)])"
            >{{visibeDays[(i-1)*7+(j-1)].getDate()}}</span>
          </div>
        </div>
      </div>
      <div class="pannel-footer">今天</div>
    </div>
  </div>
</template>
<script>
import * as utils from "./utils.js";
export default {
  directives: {
    clickOutside: {
      bind(el, bindings, vnode) {
        // 把事件绑定给document上 看一下点击的是否是当前这个元素内部
        let handler = e => {
          if (el.contains(e.target)) {
            // console.log("包含");
            // 判断面板是否已经显示出来了
            if (!vnode.context.isVisible) {
              vnode.context.focus();
            }
          } else {
            // console.log("不包含");
            if (vnode.context.isVisible) {
              vnode.context.blur();
            }
          }
        };
        el.handler = handler;
        document.addEventListener("click", handler);
      },
      unbind(el) {
        document.removeEventListener("click", el.handler);
      }
    }
  },
  props: {
    value: {
      type: Date,
      default: () => new Date()
    }
  },
  data() {
    let { year, month } = utils.getYearMonthDay(this.value);
    return {
      tiem: { year, month },
      weekDays: ["日", "一", "二", "三", "四", "五", "六"],
      isVisible: false // 控制面板是否可见
    };
  },
  computed: {
    visibeDays() {
      // 先获取当前是周几
      let { year, month } = utils.getYearMonthDay(
        utils.getDate(this.tiem.year, this.tiem.month, 1)
      );
      // 获取当前月份的第一天
      let currentFirstDay = utils.getDate(year, month, 1);
      // 先生成一个 当前 2019 5 18 2019 5 1
      // 获取当前是周几 把天数往前移动 几天
      let week = currentFirstDay.getDay();
      // 当前开始的天数
      let startDay = currentFirstDay - week * 60 * 60 * 1000 * 24;
      // 循环42天
      let arr = [];
      for (let i = 0; i < 42; i++) {
        //  依次循环出42天
        arr.push(new Date(startDay + i * 60 * 60 * 1000 * 24));
      }

      return arr;
    },
    formatDate() {
      let { year, month, day } = utils.getYearMonthDay(this.value);
      return `${year}-${month + 1}-${day}`;
    }
  },
  methods: {
    focus() {
      this.isVisible = true;
    },
    blur() {
      this.isVisible = false;
    },
    // 判断是不是当前月
    isCurrentMonth(date) {
      // 知道当前用户传的年月
      // 对比年月
      let { year, month } = utils.getYearMonthDay(
        utils.getDate(this.tiem.year, this.tiem.month, 1)
      );
      let { year: y, month: m } = utils.getYearMonthDay(date);
      return year === y && month === m;
    },
    istoday(date) {
      let { year, month, day } = utils.getYearMonthDay(new Date());
      let { year: y, month: m, day: d } = utils.getYearMonthDay(date);
      return year === y && month === m && day === d;
    },
    isSelect(date) {
      let { year, month, day } = utils.getYearMonthDay(this.value);
      let { year: y, month: m, day: d } = utils.getYearMonthDay(date);
      return year === y && month === m && day === d;
    },
    chooseDate(date) {
      this.tiem = utils.getYearMonthDay(date);
      this.$emit("input", date);
      this.blur();
    },
    prevMonth() {
        // 获取当前年月的日期
      let d = utils.getDate(this.tiem.year, this.tiem.month, 1);
      //   console.log(d)
      console.log(d.getMonth() - 1);
      d.setMonth(d.getMonth() - 1);
      this.tiem = utils.getYearMonthDay(d);
    },
    nextMonth() {
      let d = utils.getDate(this.tiem.year, this.tiem.month, 1);
      d.setMonth(d.getMonth() + 1);
      this.tiem = utils.getYearMonthDay(d);
    },
    prevYear() {
      let d = utils.getDate(this.tiem.year, this.tiem.month, 1);
      d.setFullYear(d.getFullYear() - 1);
      this.tiem = utils.getYearMonthDay(d);
    },
    nextYear() {
      let d = utils.getDate(this.tiem.year, this.tiem.month, 1);
      d.setFullYear(d.getFullYear() + 1);
      this.tiem = utils.getYearMonthDay(d);
    }
  }
};
</script>
<style lang="stylus">
.pannel {
  position: absolute;
  background-color: #fff;
  box-shadow: 2px 2px pink, -2px -2px pink;
  width: 32 * 7px;

  .pannel-nav {
    display: flex;
    justify-content: space-around;
    height: 30px;

    span {
      cursor: pointer;
      user-select: none;
    }
  }

  .pannel-content {
    .ceel {
      display: inline-flex;
      justify-content: center;
      align-items: center;
      width: 32px;
      height: 32px;
      font-weight: 700;
      box-sizing: border-box;
    }

    .ceel_day:hover, .select {
      border: 1px solid pink;
    }
  }

  .pannel-footer {
    height: 30px;
    text-align: center;
  }

  .notCurrentMonth {
    color: #ccc;
  }

  .today {
    background: red;
    color: #ffffff;
    border-radius: 4px;
  }
}
</style>