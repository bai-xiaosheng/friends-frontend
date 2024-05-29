<template>
  <van-nav-bar
      :title="title"
      left-arrow
      @click-left="onClickLeft"
      @click-right="onClickRight"
  >
    <template #right>
      <van-icon name="search" size="18"/>
    </template>
  </van-nav-bar>
<!--    <div id="content">：这是一个普通的HTML div元素，通过id属性被赋予了一个特定的标识符content。这个div元素可以包含任何你想要显示在页面上的HTML内容，并且可以被CSS样式化-->
  <div id="content">
<!--      <router-view/>：这是一个Vue Router的内置组件，它作为一个挂载点，用于渲染Vue Router中的路由所指向的组件。当用户导航到不同的路由时，Vue Router会动态地在<router-view>的位置插入或替换对应的组件-->
    <router-view/>
  </div>
  <van-tabbar route @change="onChange">
    <van-tabbar-item to="/" icon="home-o" name="index">主页</van-tabbar-item>
    <van-tabbar-item to="/team" icon="search" name="team">队伍</van-tabbar-item>
    <van-tabbar-item to="/user" icon="friends-o" name="user">个人</van-tabbar-item>
  </van-tabbar>
</template>

<script setup lang="ts">
import { useRouter } from "vue-router";
import {ref} from "vue";
import routes from "../config/route";

const router = useRouter();
const DEFAULT_TITLE = '伙伴匹配';
const title = ref(DEFAULT_TITLE);

/**
 * 根据路由切换标题
 */
router.beforeEach((to, from) => {
  const toPath = to.path;
  const route = routes.find((route) => {
    return toPath == route.path;
  })
  title.value = route?.title ?? DEFAULT_TITLE;
})

const onClickLeft = () => {
  router.back();
};

const onClickRight = () => {
  router.push('/search')
};

</script>
<!--这段代码的作用是为当前Vue组件中具有id="content"的元素添加了50像素的底部内边距。由于使用了scoped属性，这个样式不会影响到其他组件中的#content元素，即使它们具有相同的id。-->
<style scoped>
#content {
  padding-bottom: 50px;
}
</style>
