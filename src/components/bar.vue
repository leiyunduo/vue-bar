<template>
  <div id="data_assets">
    <div class="data_list-tab">
      <div class="el-tabs__header is-top">
        <div class="tabs-nav-wrap is-top is-scrollable">
          <span class="tabs-nav-prev is-disabled" @click="leftRightBtn(0)" v-if="ctrlData.iconShow === true"><i class="el-icon-arrow-left"></i></span>
          <span class="tabs-nav-next" @click="leftRightBtn(1)" v-if="ctrlData.iconShow === true"><i class="el-icon-arrow-right"></i></span>
          <div class="tabs-nav-scroll" id="divx-scroll">
            <div role="tablist" class="tabs-nav" ref="barList" id="nav-left-right" style="transform: translateX(0px);">
              <div class="tabs-active-bar is-top" :style="tabInk"></div>
              <div @click="changeTab(index)" :class="ctrlData.tabIndex === index ? 'tab-active' : ''" v-for="(item, index) in titleName" :key="index" ref="tabItem" class="tabs-item is-top">{{item}}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      ctrlData: {
        tabIndex: 0,
        iconShow: false
      },
      tabInk: '',
      screenWidth: document.body.clientWidth
    }
  },
  props: {
    titleName: {
      type: Array,
      default: () => []
    }
  },
  mounted () {
    let that = this
    // 根据屏幕大小，小于1000时来显示左右图标
    window.onresize = () => {
      return (() => {
        window.screenWidth = document.body.clientWidth
        that.screenWidth = window.screenWidth
      })()
    }
    let lengths = that.titleName.length * 140 + 300
    if (document.body.clientWidth < lengths) {
      that.ctrlData.iconShow = true
    } else {
      that.ctrlData.iconShow = false
    }
  },
  watch: {
    // 监听屏幕大小
    screenWidth (val) {
      let that = this
      setTimeout(function () {
        this.screenWidth = val
        let lengths = that.titleName.length * 140 + 300
        console.log(lengths)
        if (Number(val) < lengths) {
          that.ctrlData.iconShow = true
        } else {
          that.ctrlData.iconShow = false
          // ！！！一定要加的两句话
          // 获取最外层div
          var oUl = that.$refs.barList
          that.move(oUl, 'left', 0)
        }
        return val
      }, 400)
    }
  },
  methods: {
    changeTab (index) {
      let that = this
      this.ctrlData.tabIndex = index
      that.$emit('changeTab', index)
      this.tabInk = `transform: translate3d(${140 * index}px, 0, 0)`
    },
    // 向左滑动   // 向右滑动
    leftRightBtn (i) {
      let that = this
      // 获取最外层div
      var oUl = that.$refs.barList
      // 获取div
      var aLi = that.$refs.tabItem
	    oUl.style.width = aLi.length * (aLi[0].offsetWidth) + 'px'
      var now = -5 * (aLi[0].offsetWidth)
      if (i === 0) {
        if (oUl.offsetLeft === -660) {
          that.move(oUl, 'left', 0)
        } else if (oUl.offsetLeft >= 0) {
          that.move(oUl, 'left', 0)
        } else {
          that.move(oUl, 'left', oUl.offsetLeft - now)
        }
      } else if (i === 1) {
        var n = Math.floor((aLi.length * (aLi[0].offsetWidth) + oUl.offsetLeft) / aLi[0].offsetWidth)
        if (n <= 5) {
          that.move(oUl, 'left', 0)
        } else {
          that.move(oUl, 'left', oUl.offsetLeft + now)
        }
      }
    },
    // tabs-item的位置
    move (obj, attr, iTarget) {
      let that = this
      clearInterval(obj.timer)
      obj.timer = setInterval(function () {
        var cur = 0
        if (attr === 'opacity') {
          cur = Math.round(parseFloat(that.getStyle(obj, attr)) * 100)
        } else {
          cur = parseInt(that.getStyle(obj, attr))
        }
        var speed = (iTarget - cur) / 6
        speed = speed > 0 ? Math.ceil(speed) : Math.floor(speed)
        if (iTarget === cur) {
          clearInterval(obj.timer)
        } else {
          obj.style[attr] = cur + speed + 'px'
        }
      }, 30)
    },
    getStyle (obj, name) {
      // currentStyle  获取外部的样式  只兼容IE,不兼容火狐和谷歌
      if (obj.currentStyle) {
        return obj.currentStyle[name]
      } else {
        // getComputedStyle  获取外部的样式  兼容火狐谷歌,不兼容IE
        return getComputedStyle(obj, false)[name]
      }
    }
  }
}
</script>

<style lang="less" scoped>
#data_assets {
  width: 100%;
  min-height: 100%;
  margin: 0 auto;
  background: #f5f7f8;
  .data_list-tab {
    .el-tabs__header {
      padding: 0;
      position: relative;
      margin: 0 0 15px;
      .tabs-nav-wrap {
        box-sizing: border-box;
        overflow: hidden;
        margin-bottom: -1px;
        position: relative;
        padding: 0 20px;
        background: #fff;
        .tabs-nav-scroll {
          overflow: hidden;
          .tabs-nav {
            white-space: nowrap;
            position: relative;
            transition: transform .3s;
            float: left;
            z-index: 2;
            .tabs-active-bar {
              width: 140px;
              position: absolute;
              bottom: 0;
              left: 0;
              height: 2px;
              background-color: #eb6100;
              z-index: 1;
              transition: transform .3s cubic-bezier(.645,.045,.355,1);
              list-style: none;
            }
            .tabs-item {
              text-align: center;
              height: 60px;
              width: 140px;
              box-sizing: border-box;
              line-height: 60px;
              display: inline-block;
              list-style: none;
              font-size: 16px;
              font-weight: 500;
              color: #303133;
              position: relative;
              cursor: pointer;
            }
            .tab-active {
              color: #eb6100;
            }
            .tabs-item:hover {
              color: #eb6100;
            }
          }
        }
        .tabs-nav-prev {
          left: 0;
        }
        .tabs-nav-next, .tabs-nav-prev {
          position: absolute;
          cursor: pointer;
          line-height: 44px;
          font-size: 22px;
          color: #909399;
          top: 8px;
        }
        .tabs-nav-next {
          right: 0;
        }
      }
      .tabs-nav-wrap::after {
        content: "";
        position: absolute;
        left: 0;
        bottom: 0;
        width: 100%;
        height: 2px;
        background-color: #E4E7ED;
        z-index: 1;
      }
    }
  }
}
</style>
