<!--template：这部分包含了组件的HTML结构，可以包含元素、属性、指令、插值表达式等，用于告诉Vue如何渲染组件的界面。-->
<!--script：这部分包含了组件的JavaScript逻辑，可以定义响应式数据、方法、生命周期钩子等。-->
<!--style：这部分包含了组件的CSS样式，可以定义组件的样式规则。-->

<!--<template>定义了组件的HTML结构-->
<template>
<!-- van-cell 创建一个单元格，标题为心动模式，包含一个开关van-switch，绑定了isMatchMode   -->
  <van-cell center title="心动模式">
    <template #right-icon>
      <van-switch v-model="isMatchMode" size="24" />
    </template>
  </van-cell>
<!--    ser-card-list是一个自定义组件，用于展示用户列表，它接收userList作为属性来展示用户数据，loading属性表示数据是否正在加载。
        van-empty组件用于在没有用户数据时显示一个空状态-->
  <user-card-list :user-list="userList" :loading="loading"/>
  <van-empty v-if="!userList || userList.length < 1" description="数据为空"/>
</template>
<!--<script setup>部分是Vue 3的Composition API的语法，用于定义组件的逻辑：-->
<script setup lang="ts">
import { ref, watchEffect } from 'vue';
// 自定义的HTTP请求函数myAxios
import myAxios from "../plugins/myAxios";
import {Toast} from "vant";
import UserCardList from "../components/UserCardList.vue";
import {UserType} from "../models/user";
// ref是一个Vue 3的函数，它用于创建一个响应式的引用对象。<boolean>：这是TypeScript的类型注解，它告诉TypeScript，ref函数创建的响应式引用对象将存储一个布尔值（true或false）。
// false：这是ref函数的参数，它初始化了响应式引用对象的值为false。
const isMatchMode = ref<boolean>(false);

const userList = ref([]);
const loading = ref(true);

/**
 * 加载数据
 * 定义了一个异步函数loadData来根据isMatchMode的值请求不同的用户数据。如果是心动模式，它会请求/user/match接口；否则，它会请求/user/recommend接口。请求成功后，它会将用户数据解析并存储到userList中，并更新loading状态
 */
const loadData = async () => {
  let userListData;
  loading.value = true;
  // 心动模式，根据标签匹配用户
  if (isMatchMode.value) {
    const num = 10;
    userListData = await myAxios.get('/user/match', {
      params: {
        num,
      },
    })
        .then(function (response) {
          console.log('/user/match succeed', response);
          return response?.data;
        })
        .catch(function (error) {
          console.error('/user/match error', error);
          Toast.fail('请求失败');
        })
  } else {
    // 普通模式，直接分页查询用户
    userListData = await myAxios.get('/user/recommend', {
      params: {
        pageSize: 8,
        pageNum: 1,
      },
    })
        .then(function (response) {
          console.log('/user/recommend succeed', response);
          return response?.data;
        })
        .catch(function (error) {
          console.error('/user/recommend error', error);
          Toast.fail('请求失败');
        })
  }
  // 判断返回数据是否存在
  if (userListData) {
    userListData.forEach((user: UserType) => {
      //   如果存在标签，将json格式的数据转换为一个JavaScript对象
      if (user.tags) {
        user.tags = JSON.parse(user.tags);
      }
    })
    //   在遍历并处理完所有的用户数据后，这行代码将处理后的userListData数组赋值给响应式引用对象userList的value属性。
    //   由于userList是使用Vue 3的ref函数创建的，所以它的value属性是响应式的。这意味着当userList的值更新后，任何依赖于userList的Vue组件都会重新渲染以反映新的数据。
    userList.value = userListData;
  }
  loading.value = false;
}
// 使用watchEffect来监听响应式变量的变化，并在组件挂载时自动调用loadData函数加载数据。
watchEffect(() => {
  loadData();
})

</script>

<style scoped>

</style>
