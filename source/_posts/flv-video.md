---
title: 'Flv Video'
entitle: flv-video
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
comments: true
date: 2020-11-13 14:36:34
timestamp:
tags:
  - Web
keywords:
description: 通过 flv.js 自定义一个 video
photos:
  - /img/tags/web.jpg
---

由于年底谷歌浏览器将不在支持 Flash Player 了，为了避免到时候出现问题，所以决定使用 flv.js 来播放视频流了。

flv.js 不像 video.js 一样，能在 UI 方面帮我们处理一些问题。所以对 video 进行了进一步封装，自定了 loading 和 切换全屏操作。接下来就看看具体如何操作吧。直接上代码：

```html
<template>
  <div ref="videoContainer" class="video-container" @mouseover="handleControls('over')" @mouseleave="handleControls('leave')">
    <video ref="video" class="video" muted>
      Sorry, your browser doesn't support embedded videos.
    </video>

    <!--- Loading --->
    <div v-if="isShowLoading" class="loading-spinner"></div>

    <!--- 播放器底部控制栏，提供切换全屏功能 --->
    <transition name="fade">
      <div class="video-bottom-control" v-show="!hideControl">
        <i v-if="isFullScreen" class="el-icon-copy-document icon" @click="handleExitFullScreen"></i>
        <i v-else class="el-icon-full-screen icon" @click="handleFullScreen"></i>
      </div>
    </transition>
  </div>
</template>

<script>
import flvjs from 'flv.js'

export default {
  name: 'FlvVideo',
  props: {
    sourceUrl: String
  },
  data() {
    return {
      player: null,
      hideControl: true,
      isFullScreen: false,
      // 通过 poster 设置 loading 时，存在图片尺寸变化的问题，所以需要自定义 loading 效果，这里搬了下 video.js 的 loading 效果
      isShowLoading: false
    }
  },
  beforeDestroy() {
    this.destroyVideo()
  },
  mounted() {
    this.playVideo()
    // 由于全屏状态下无法监听 onkeydown 事件，所以通过 onresize 来监听 ESC 退出全屏事件
    window.onresize = this.dobounce(() => {
      this.isFullScreen = this.checkFullScreen()
    })
  },
  methods: {
    dobounce(fn) {
      let timer = null
      return function() {
        timer && clearTimeout(timer)
        timer = setTimeout(() => {
          fn.apply(this, arguments)
        }, 100)
      }
    },
    playVideo() {
      if (flvjs.isSupported()) {
        this.isShowLoading = true

        const videoElement = this.$refs.video
        // 切换视频流时，先将播放器销毁
        this.destroyVideo()
        this.player = flvjs.createPlayer({
          type: 'flv',
          isLive: true,
          url: this.sourceUrl,
          hasAudio: false,
          hasVideo: true
        })
        this.player.attachMediaElement(videoElement)
        this.player.load()
        this.player.play()

        // 通过监听 SCRIPTDATA_ARRIVED 事件，延时隐藏 loading
        this.player.on(flvjs.Events.SCRIPTDATA_ARRIVED, () => {
          setTimeout(() => {
            this.isShowLoading = false
          }, 1000)

          this.player.off(flvjs.Events.SCRIPTDATA_ARRIVED, () => {})
        })
      } else {
        this.$message.error('浏览器不支持视频预览功能！')
      }
    },
    destroyVideo() {
      if (this.player) {
        this.player.pause()
        this.player.unload()
        this.player.detachMediaElement()
        this.player.destroy()
        this.player = null
      }
    },
    // 监听离开或者进入视频区域隐藏或者展示控制栏
    handleControls(flag) {
      this.hideControl = flag === 'leave'
    },
    // 通过 video 的父容器全屏来达到 video 全屏的效果
    handleFullScreen() {
      const dom = this.$refs.videoContainer
      if (dom.requestFullscreen) {
        dom.requestFullscreen()
      } else if (dom.mozRequestFullScreen) {
        dom.mozRequestFullScreen()
      } else if (dom.webkitRequestFullScreen) {
        dom.webkitRequestFullScreen()
      }
    },
    handleExitFullScreen() {
      if (document.exitFullscreen) {
        document.exitFullscreen()
      } else if (document.mozCancelFullScreen) {
        document.mozCancelFullScreen()
      } else if (document.webkitCancelFullScreen) {
        document.webkitCancelFullScreen()
      }
    },
    checkFullScreen() {
      const isFull =
        document.fullscreenElement ||
        document.mozFullScreenElement ||
        document.webkitFullscreenElement

      return !!isFull
    }
  }
}
</script>

<style lang="scss" scoped>
.video-container {
  flex: 1;
  position: relative;

  .video {
    width: 100%;
    height: 100%;
    background-color: #000;
    border-radius: 8px;
  }

  .video-bottom-control {
    position: absolute;
    width: 100%;
    height: 30px;
    left: 0;
    bottom: 0;
    background-color: rgba(43, 51, 63, 0.7);
    display: flex;
    align-items: center;
    justify-content: flex-end;

    .icon {
      font-size: 16px;
      color: #fff;
      margin-right: 20px;
    }
  }
}

/* 控制栏隐藏动画 */
.fade-enter-active {
  transition: all 0.1s;
}
.fade-leave-active {
  transition: all 1s;
}
.fade-enter,
.fade-leave-to {
  visibility: hidden;
  opacity: 0;
}

$primary-background-color: #2b333f !default;
$primary-background-transparency: 0.7 !default;
$secondary-background-color: lighten($primary-background-color, 33%) !default;
$secondary-background-transparency: 0.5 !default;

.loading-spinner {
  position: absolute;
  top: 50%;
  left: 50%;
  margin: -25px 0 0 -25px;
  opacity: 0.85;
  text-align: left;
  border: 6px solid
    rgba($primary-background-color, $primary-background-transparency);
  box-sizing: border-box;
  background-clip: padding-box;
  width: 50px;
  height: 50px;
  border-radius: 25px;
  visibility: hidden;
  animation: spinner-show 0s linear 0.3s forwards;
}

.loading-spinner:before,
.loading-spinner:after {
  content: '';
  position: absolute;
  margin: -6px;
  box-sizing: inherit;
  width: inherit;
  height: inherit;
  border-radius: inherit;
  // Keep 100% opacity so they don't show through each other
  opacity: 1;
  border: inherit;
  border-color: transparent;
  border-top-color: white;
  animation: spinner-spin 1.1s cubic-bezier(0.6, 0.2, 0, 0.8) infinite,
    spinner-fade 1.1s linear infinite;
}

@keyframes spinner-show {
  to {
    visibility: visible;
  }
}

@keyframes spinner-spin {
  100% {
    transform: rotate(360deg);
  }
}

@keyframes spinner-fade {
  0% {
    border-top-color: $secondary-background-color;
  }
  20% {
    border-top-color: $secondary-background-color;
  }
  35% {
    border-top-color: white;
  }
  60% {
    border-top-color: $secondary-background-color;
  }
  100% {
    border-top-color: $secondary-background-color;
  }
}
</style>
```

> 目前不是所有浏览器切换全屏方法都返回 promise（IE 和 Safair 都不支持）。若在不监听 window.onresize 事件的情况下，需要在切换全屏操作后做一些处理时，需要注意这一点。 [Can I use](https://caniuse.com/?search=requestFullscreen%3A%20Returns%20a%20Promise)