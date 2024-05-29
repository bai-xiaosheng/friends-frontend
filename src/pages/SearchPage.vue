<template>
  <form action="/">
<!--      <van-search>是一个搜索框组件，它绑定了v-model来双向绑定searchText变量，允许用户输入搜索内容。同时，它还绑定了@search和@cancel事件，分别用于处理搜索和取消操作-->
    <van-search
        v-model="searchText"
        show-action
        placeholder="请输入要搜索的标签"
        @search="onSearch"
        @cancel="onCancel"
    />
  </form>
<!--    <van-divider>是一个分割线组件，用于视觉上分隔内容。-->
  <van-divider content-position="left">已选标签</van-divider>
<!--  一个条件渲染的<div>，当activeIds数组长度为0时显示提示信息。-->
  <div v-if="activeIds.length === 0">请选择标签</div>
  <van-row gutter="16" style="padding: 0 16px">
    <van-col v-for="tag in activeIds">
      <van-tag closeable size="small" type="primary" @close="doClose(tag)">
        {{ tag }}
      </van-tag>
    </van-col>
  </van-row>
  <van-divider content-position="left">选择标签</van-divider>
<!--    <van-tree-select>是一个树形选择器组件，用于选择标签，它绑定了v-model:active-id和v-model:main-active-index来双向绑定已选中的标签ID列表和当前激活的索引。-->
  <van-tree-select
      v-model:active-id="activeIds"
      v-model:main-active-index="activeIndex"
      :items="tagList"
  />
  <div style="padding: 12px">
    <van-button block type="primary" @click="doSearchResult">搜索</van-button>
  </div>
</template>

<script setup lang="ts">
import {ref} from 'vue';
import {useRouter} from "vue-router";

const router = useRouter()

const searchText = ref('');

const originTagList = [
    {
        text: '编程语言',
        children: [
            {text: 'java', id: 'java'},
            {text: 'c++', id: 'c++'},
            {text: 'python', id: 'python'},
            {text: 'c', id: 'c'},
            {text: 'go', id: 'go'},
            {text: '其它', id: '其它'},
        ],
    },
    {
  text: '性别',
  children: [
    {text: '男', id: '男'},
    {text: '女', id: '女'},
  ],
},

]

// 标签列表
let tagList = ref(originTagList);

/**
 * 搜索过滤
 * @param val
 */
const onSearch = (val) => {
  tagList.value = originTagList.map(parentTag => {
    const tempChildren = [...parentTag.children];
    const tempParentTag = {...parentTag};
    tempParentTag.children = tempChildren.filter(item => item.text.includes(searchText.value));
    return tempParentTag;
  });

}
const onCancel = () => {
  searchText.value = '';
  tagList.value = originTagList;
};

// 已选中的标签
const activeIds = ref([]);
const activeIndex = ref(0);

// 移除标签
const doClose = (tag) => {
  activeIds.value = activeIds.value.filter(item => {
    return item !== tag;
  })
}

/**
 * 执行搜索
 */
const doSearchResult = () => {
  router.push({
    path: '/user/list',
    query: {
      tags: activeIds.value
    }
  })
}

</script>

<style scoped>

</style>
