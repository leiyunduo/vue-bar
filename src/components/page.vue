<template>
  <div id="data_assets">
    <div class="data_list-tab">
      <Bar :titleName='tabList' @changeTab='changeTab' />
    </div>
  </div>
</template>

<script>
// 导航组件
import Bar from './bar'
export default {
  data () {
    return {
      tabList: ['我是按钮1', '我是按钮2', '我是按钮3', '我是按钮4', '我是按钮5', '我是按钮6', '我是按钮7', '我是按钮8', '我是按钮9']
    }
  },
  components: {
    Bar
  },
  methods: {
    changeTab (index) {
      console.log(index)
    }
  }
}
</script>
