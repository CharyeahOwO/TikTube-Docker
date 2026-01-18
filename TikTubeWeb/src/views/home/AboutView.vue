<template>
  <v-container class="py-8">
    <!-- 页面标题区域 -->
    <v-row align="center" class="mb-6">
      <v-col>
        <div class="d-flex align-center">
          <v-icon icon="mdi-help-circle" color="red" size="32" class="mr-3"></v-icon>
          <h1 class="text-h4 font-weight-bold mb-0">关于我们</h1>
        </div>
        <p class="text-body-1 text-medium-emphasis mt-2 ml-9">About TikTube</p>
      </v-col>
    </v-row>

    <!-- Logo和介绍区域 -->
    <v-card class="mb-8 rounded-lg" elevation="3">
      <v-card-text class="pa-6">
        <v-row align="center">
          <v-col cols="12" md="4" class="text-center">
            <v-img
              :height="220"
              src="logo.png"
              alt="TikTube Logo"
              class="mx-auto mb-4"
              contain
            ></v-img>
          </v-col>
          <v-col cols="12" md="8">
            <h2 class="text-h5 font-weight-bold mb-4">TikTube - 弹幕视频分享平台</h2>
            <p class="text-body-1 mb-4">
              TikTube是一个创新的视频分享平台，结合了短视频的趣味性和弹幕互动的社交体验。我们致力于为用户提供一个简单、流畅且功能丰富的视频观看和分享环境。
            </p>
            <p class="text-body-1 mb-4">
              通过TikTube，您可以发现有趣的视频内容，与其他用户通过弹幕实时互动，分享您的创意作品，并与志同道合的创作者建立联系。
            </p>
            <div class="d-flex flex-wrap gap-2 mt-4">
              <v-chip color="red" text-color="white" size="large">
                <v-icon start>mdi-video</v-icon>
                视频分享
              </v-chip>
              <v-chip color="blue" text-color="white" size="large">
                <v-icon start>mdi-message-text</v-icon>
                弹幕互动
              </v-chip>
              <v-chip color="green" text-color="white" size="large">
                <v-icon start>mdi-account-group</v-icon>
                社区交流
              </v-chip>
            </div>
          </v-col>
        </v-row>
      </v-card-text>
    </v-card>

    <!-- 开源信息区域 -->
    <v-card class="mb-8 rounded-lg" elevation="2">
      <v-card-title class="text-h5 font-weight-bold py-4 px-6 bg-primary text-white">
        <v-icon icon="mdi-github" class="mr-2"></v-icon>
        开源地址
      </v-card-title>
      <v-card-text class="pa-6">
        <p class="text-body-1 mb-4">
          本项目是基于原项目进行 Docker 化改造的版本，欢迎访问我们的代码仓库：
        </p>

        <v-list>
          <v-list-item>
            <template v-slot:prepend>
              <v-avatar color="grey-lighten-3" size="36">
                <v-icon color="black">mdi-github</v-icon>
              </v-avatar>
            </template>
            <v-list-item-title class="text-subtitle-1 font-weight-medium">GitHub (Docker 版本)</v-list-item-title>
            <v-list-item-subtitle>
              <a
                href="https://github.com/CharyeahOwO/TikTube-Docker"
                target="_blank"
                class="text-decoration-none"
              >
                https://github.com/CharyeahOwO/TikTube-Docker
              </a>
            </v-list-item-subtitle>
          </v-list-item>
        </v-list>
      </v-card-text>
    </v-card>

    <!-- 技术栈区域 -->
    <v-card class="mb-8 rounded-lg" elevation="2">
      <v-card-title class="text-h5 font-weight-bold py-4 px-6 bg-secondary text-white">
        <v-icon icon="mdi-code-tags" class="mr-2"></v-icon>
        技术栈
      </v-card-title>
      <v-card-text class="pa-6">
        <v-row>
          <v-col cols="12" md="6">
            <h3 class="text-subtitle-1 font-weight-bold mb-3">前端</h3>
            <v-chip-group>
              <v-chip color="primary" variant="outlined" class="ma-1">Vue 3</v-chip>
              <v-chip color="primary" variant="outlined" class="ma-1">Vuetify</v-chip>
              <v-chip color="primary" variant="outlined" class="ma-1">Pinia</v-chip>
              <v-chip color="primary" variant="outlined" class="ma-1">Vue Router</v-chip>
            </v-chip-group>
          </v-col>
          <v-col cols="12" md="6">
            <h3 class="text-subtitle-1 font-weight-bold mb-3">后端</h3>
            <v-chip-group>
              <v-chip color="success" variant="outlined" class="ma-1">Spring Boot</v-chip>
              <v-chip color="success" variant="outlined" class="ma-1">MyBatis</v-chip>
              <v-chip color="success" variant="outlined" class="ma-1">MySQL</v-chip>
              <v-chip color="success" variant="outlined" class="ma-1">Docker</v-chip>
            </v-chip-group>
          </v-col>
        </v-row>
      </v-card-text>
    </v-card>

    <!-- 联系我们区域 -->
    <v-card class="mb-8 rounded-lg" elevation="2">
      <v-card-title class="text-h5 font-weight-bold py-4 px-6 bg-info text-white">
        <v-icon icon="mdi-email" class="mr-2"></v-icon>
        联系我们
      </v-card-title>
      <v-card-text class="pa-6">
        <p class="text-body-1 mb-4">
          如果您有任何问题、建议或合作意向，欢迎通过以下方式与我们联系：
        </p>
        <div class="d-flex align-center mb-3">
          <v-icon color="primary" class="mr-3">mdi-github</v-icon>
          <span class="text-body-1">GitHub Issues: </span>
          <a
            href="https://github.com/CharyeahOwO/TikTube-Docker/issues"
            target="_blank"
            class="text-decoration-none ml-2"
          >
            提交问题或建议
          </a>
        </div>
        <div class="d-flex align-center mb-3">
          <v-icon color="red" class="mr-3">mdi-email</v-icon>
          <span class="text-body-1">邮箱: </span>
          <a
            href="mailto:2971087216@qq.com"
            class="text-decoration-none ml-2"
          >
            2971087216@qq.com
          </a>
        </div>
      </v-card-text>
    </v-card>

    <!-- 原作者致谢区域 -->
    <v-card class="mb-8 rounded-lg" elevation="2">
      <v-card-title class="text-h5 font-weight-bold py-4 px-6 bg-warning">
        <v-icon icon="mdi-heart" class="mr-2"></v-icon>
        原作者致谢
      </v-card-title>
      <v-card-text class="pa-6">
        <p class="text-body-1 mb-4">
          本项目基于 <strong>PuZhiweizuishuai</strong> 的 TikTube 项目进行 Docker 化改造，在此特别感谢原作者的辛勤付出！
        </p>
        <div class="d-flex align-center mb-3">
          <v-icon color="black" class="mr-3">mdi-github</v-icon>
          <span class="text-body-1">原项目地址: </span>
          <a
            href="https://github.com/PuZhiweizuishuai/TikTube"
            target="_blank"
            class="text-decoration-none ml-2"
          >
            https://github.com/PuZhiweizuishuai/TikTube
          </a>
        </div>
        <v-alert type="warning" variant="tonal" class="mt-4">
          <strong>⚠️ 重要提示：</strong> 本项目为 Docker 改造版本，如遇问题请在本仓库提交 Issue，<strong>请勿向原作者仓库提交 Issues</strong>，以免给原作者带来困扰。
        </v-alert>
      </v-card-text>
    </v-card>

    <!-- 页脚版权信息 -->
    <div class="text-center mt-8 text-body-2 text-medium-emphasis">
      <p>
        <a href="https://mulingowo.cn/" target="_blank"><strong>拾光物语</strong></a> ©2020 -
        {{ new Date().getFullYear() }}
      </p>
      <p>version: 1.3.1-docker 2026-01-18</p>
    </div>
  </v-container>
</template>

<script>
export default {
  name: 'AboutView',
  created() {
    document.title = '关于我们 - TikTube'
  },
}
</script>

<style scoped>
.gap-2 {
  gap: 8px;
}

a {
  color: #1867c0;
  transition: color 0.2s ease;
}

a:hover {
  color: #5cbbf6;
}
</style>