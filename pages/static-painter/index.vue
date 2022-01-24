<template>
  <view class="index">
    <painter
      :customStyle="'margin-left:40rpx'"
      :palette="template"
      @imgOK="onImgOK"
    />
    <image
      :src="imagePath"
      mode="aspectFill"
      style="height: 1000rpx; width: 654rpx; margin-left: 48rpx"
    />
    <button class="save-button" @click="saveImage">保存</button>
  </view>
</template>

<script>
import Card from "./palette/card";

export default {
  data() {
    return {
      template: {},
      imagePath: "",
    };
  },
  onLoad() {
    this.template = new Card().palette();
  },
  methods: {
    saveImage() {
      if (this.imagePath) {
        uni.saveImageToPhotosAlbum({
          filePath: this.imagePath,
        });
      }
    },
    onImgOK(e) {
      this.imagePath = e.detail.path;
    },
  },
};
</script>

<style>
.save-button {
  position: relative;
  margin-top: 40rpx;
}
</style>
