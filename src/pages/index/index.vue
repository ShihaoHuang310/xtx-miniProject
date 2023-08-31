<script setup lang="ts">
import { ref } from 'vue'
import { onLoad } from '@dcloudio/uni-app'

import XtxSwiper from '@/components/XtxSwiper.vue'
import XtxGuess from '@/components/XtxGuess.vue'

import CustomNavbar from './components/CustomNavbar.vue'
import CategoryPanel from './components/CategoryPanel.vue'
import HotPanel from './components/HotPanel.vue'
import PageSkeleton from './components/PageSkeleton.vue'

import { getHomeBannerAPI, getHomeCategoryAPI, getHomeHotAPI } from '@/services/home'
import type { BannerItem, CategoryItem, HotItem } from '@/types/home'
import type { XtxGuessInstance } from '@/types/component'

// 获取猜你喜欢组件实例
const guessRef = ref<XtxGuessInstance>()
const bannerList = ref<BannerItem[]>([])
const categoryList = ref<CategoryItem[]>([])
const hotList = ref<HotItem[]>([])
// 下拉刷新状态
const isTriggered = ref(false)
//加载
const isLoading = ref(false)

const getHomeBannerList = async () => {
  const res = await getHomeBannerAPI()
  console.log('banner', res)
  bannerList.value = res.result
}
const getHomeCategoryList = async () => {
  const res = await getHomeCategoryAPI()
  console.log('categoryList', res)
  categoryList.value = res.result
}
const getHotList = async () => {
  const res = await getHomeHotAPI()
  console.log('categoryList', res)
  hotList.value = res.result
}

onLoad(async () => {
  isLoading.value = true
  await Promise.all([getHomeBannerList(), getHomeCategoryList(), getHotList()])
  isLoading.value = false
})
// 滚动触底事件
const onScrolltolower = () => {
  guessRef.value?.getMore()
}
// 自定义下拉刷新被触发
const onRefresherrefresh = async () => {
  // 开启动画
  isTriggered.value = true
  // 重置猜你喜欢组件数据
  guessRef.value?.resetData()
  // 加载数据
  await Promise.all([getHomeBannerList(), getHomeCategoryList(), getHotList()])
  // 关闭动画
  isTriggered.value = false
}
</script>

<template>
  <CustomNavbar />
  <!-- 滚动容器 -->
  <scroll-view
    class="scroll-view"
    @refresherrefresh="onRefresherrefresh"
    :refresher-triggered="isTriggered"
    refresher-enabled
    scroll-y
    @scrolltolower="onScrolltolower"
  >
    <PageSkeleton v-if="isLoading" />
    <template v-else>
      <XtxSwiper :list="bannerList" />
      <CategoryPanel :list="categoryList" />
      <HotPanel :list="hotList" />
      <!-- 猜你喜欢 -->
      <XtxGuess ref="guessRef" />
    </template>
  </scroll-view>
</template>

<style lang="scss">
page {
  background-color: #fff;
  height: 100%;
  display: flex;
  flex-direction: column;
}
.scroll-view {
  flex: 1;
}
</style>
