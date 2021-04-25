<template>
  <div id="app" v-loading.fullscreen="!ready">
    <keep-alive :include="getCacheLayout">
      <template v-if="ready">
        <component :is="layout"></component>
      </template>
    </keep-alive>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import Component from "vue-class-component";
import {State} from "vuex-class";
import DefaultLauout from "~/layouts/default.layout.vue";
import WorkspaceLayout from "~/layouts/workspace.layout.vue";
import WaterMark from "watermark-dom";
import {Watch} from "vue-property-decorator";

@Component({
  components: {
    default: DefaultLauout,
    workspace: WorkspaceLayout,
  },
})
export default class App extends Vue {
  @State ready: boolean;
  @State layout: string;
  @State userToken: string;
  @State userData: any;

  get getCacheLayout() {
    let cache: Array<string> = [];

    if (this.userToken) {
      cache.push("WorkSpaceLayout");
    }

    return cache;
  }

  @Watch("userToken", {immediate: true})
  private tokenChange(value) {
    if (!value) {
      WaterMark.load({watermark_txt: " "});
    } else {
      const {userName, realName} = this.userData;
      if (userName && realName) {
        const markText = realName + userName;
        WaterMark.init({watermark_txt: markText, watermark_fontsize: "12px", watermark_alpha: 0.08});
      }
    }
  }
}
</script>

<style lang="less">
@import "normalize-css/normalize.css";
@import "@zct1989/vue-component/dist/index.css";
@import "assets/styles/layout.less";
@import "assets/styles/common.less";
@import "assets/styles/default.less";
@import "assets/styles/theme.less";
</style>
