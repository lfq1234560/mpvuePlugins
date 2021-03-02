<template>
  <div>
    <!-- 换图区域 -->
    <div class="change-img">
      <img :src="url" mode="aspectFill" alt="">
      <button @click="change">换图</button>
    </div>
    <!-- 创建海报 -->
    <div class="create-poster">
      <button  @click="createPoster">生成海报</button>
      <img mode="widthFix" :src="posterUrl" alt="">
      <canvas style="width:375px;height:600px;background:red" canvas-id="canvas"></canvas>
    </div>
  </div>
</template>

<script>
  export default {

    data() {
      return {
        //上传图片的url
        url: "",
        //海报图片
        posterUrl:"",

      }
    },
    methods: {
      /**
       *description:换图片
       */
      change() {
        let _this = this;
        // 调用选择图片或者使用拍照api
        wx.chooseImage({
          count: 1,
          sizeType: ['original', 'compressed'],
          sourceType: ['album', 'camera'],
          success: function (res) {
            let tempFilePaths = res.tempFilePaths; //图片的本地临时路径
            _this.url = tempFilePaths[0];
            wx.showToast({
              title: '更换成功',
              icon: 'none',
            });
          }
        })
      },
      /**
       * description:创建海报
       */
      createPoster() {
        //获取canvas上下文对象
        let canvasContext = wx.createCanvasContext("canvas", this);
        console.log(this.url);
        //绘制顶部图片
        canvasContext.drawImage(this.url, 87.5, 0, 200, 100);
        // 绘制(中山宴会)文字
        canvasContext.setFontSize(20);
        canvasContext.setFillStyle("yellow");
        canvasContext.setTextAlign("center");
        canvasContext.fillText("中山宴会", 187.5, 130);
        //绘制(时间)文字
        canvasContext.setFontSize(20);
        canvasContext.setFillStyle("blue");
        canvasContext.fillText("2022年03月01日  16时28分", 187.5, 160);
        canvasContext.setTextAlign("center");
        canvasContext.draw(false, () => {
          wx.canvasToTempFilePath({
            x: 0,
            y: 0,
            width: 375,
            height: 650,
            destWidth: 1125,
            destHeight: 1950,
            canvasId: "canvas",
            fileType: "jpg",
            quality: 1.0,
            success: (res) => {
              this.posterUrl=res.tempFilePath //海报图片
            },
            fail: () => {},
          }, this);

        });

      }
    }

  }

</script>

<style scoped>
  /* 换图片区域 */
  .change-img {
    position: relative;
    margin-bottom: 50rpx;
  }

  .change-img img {
    width: 400rpx;
    height: 400rpx;
    border: 1rpx solid #ccc;
    display: block;
    border-radius: 30rpx;
    margin: 0 auto;
  }

  .change-img button {
    width: 150rpx;
    height: 50rpx;
    line-height: 50rpx;
    position: absolute;
    top: 372rpx;
    left: 310rpx;

  }
  /* 创建海报 */
  .create-poster button {
    margin-bottom: 100rpx;
  }
  .create-poster img{
    width:100%;
    height: 100rpx;
  }
</style>
