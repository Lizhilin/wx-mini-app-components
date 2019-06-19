<template>
  <div class="NextLoad">
    <ul v-if="list.length" class="list">
      <template v-for="item in list">
        <slot v-bind="item" />
      </template>
    </ul>
    <NoMore v-if="!hasMore && list.length" />
    <ViewNone v-if="viewNoneShow"
              :viewnone="ViewNoneConfig"
              :viewaction="tapViewNone"
    />
  </div>
</template>

<script>
/**
 * @Description: 下拉刷新容器组件
 * @author Lizhilin
 * @example:
    <NextLoad :list="list"
             @getList="getList"
             @setPage="setPage"
             @resetList="resetList"
             >
      <template slot-scope="item">
        <li class="item line-1px-b boxBg">
          {{ item.xxx }}
        </li>
      </template>
    </NextLoad>

 async getList({ isLock, success, error }) {
   if (isLock()) { return }
   let data
   try {
     data = await API.getIntegralDetails({ pageSize, pageNo }).then(e => {
       if (e.code === 1) return e.data
       else throw e
     })
   } catch (e) {
     console.log('😡 getList', e)
     error(e)
     return
   }
   const { content, currentPageNo, totalCount } = data
   this.list = this.list.concat(content)
   const toTheLastPage = !(currentPageNo < Math.ceil(totalCount / pageSize))
   success({
     toTheLastPage,
     currentPageNo,
     totalCount,
     noDataText: '您还没有积分记录',
     noDataType: 'noIntegralData',
   })
 },
 setPage(page) {
   pageNo = page
 },
 resetList() { this.list = [] }
 */

import ViewNone from '@/components/base/ViewNone'
import NoMore from '@/components/base/NoMore'
import ErrorMixin from '@/mixin/ErrorMixin'
import * as utils from '@/utils'

export default {
  name: 'NextLoad',
  components: { NoMore, ViewNone },
  mixins: [ErrorMixin],
  props: {
    list: { default: () => [], type: Array }
  },
  data() {
    return {
      hasMore: true,
      closeGet: false
    }
  },
  mounted() {
    this.getData()
  },
  onReachBottom() { // 上拉加载
    this.getData()
  },
  async onPullDownRefresh() { // 下拉刷新
    if (this.closeGet) { return }
    this.initProps()
    this.getData()
  },
  methods: {
    getData() {
      if (this.closeGet) { return }
      this.$listeners.getList({
        // 锁定处理 isLock
        isLock: () => {
          if (this.closeGet || !this.hasMore) {
            return true
          } else {
            this.closeGet = true
            return false
          }
        },
        // 加载成功
        success: ({ toTheLastPage, currentPageNo, totalCount, noDataText = '暂无数据', noDataType = 'noListData' }) => {
          utils.App.stopPullDownRefresh()
          if (toTheLastPage) { // 到最后一页
            this.hasMore = false
            if (totalCount === 0) this.errHandler(-10001, noDataText, noDataType)
          } else { // 还未到最后一页
            this.hasMore = true
            this.$listeners.setPage(currentPageNo + 1) // 页码加一
          }
          this.closeGet = false
        },
        // 失败处理
        error: (e) => {
          if (e.code !== -1000) {
            this.errHandler(-10002, '数据加载错误', 'netError')
          }
          this.closeGet = false
        }
      })
    },
    initProps() {
      this.$listeners.setPage(1)
      this.viewNoneShow = false
      this.hasMore = true
      this.closeGet = false
      this.$listeners.resetList()
    },
    tapViewNone() {
      this.initProps()
      this.getData()
    }
  }
}
</script>
