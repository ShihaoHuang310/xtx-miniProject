<script setup lang="ts">
import { ref } from 'vue'
import { onLoad } from '@dcloudio/uni-app'

import XtxSwiper from '@/components/XtxSwiper.vue'
import CustomNavbar from './components/CustomNavbar.vue'
import CategoryPanel from './components/CategoryPanel.vue'

import { getHomeBannerAPI, getHomeCategoryAPI } from '@/services/home'
import type { BannerItem, CategoryItem } from '@/types/home'
const bannerList = ref<BannerItem[]>([])
const categoryList = ref<CategoryItem[]>([])

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

onLoad(() => {
  getHomeBannerList()
  getHomeCategoryList()
})
</script>

<template>
  <CustomNavbar />
  <XtxSwiper :list="bannerList" />
  <CategoryPanel :list="categoryList" />
</template>

<style lang="scss">
//
</style>
