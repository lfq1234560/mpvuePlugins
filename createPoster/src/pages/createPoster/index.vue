<template>
  <div>
    <!-- 画板 -->
    <div class="canvas-con" v-if="!isShowPoster">
      <canvas style="width:375px;height:200px;" canvas-id="canvas"></canvas>
    </div>
    <!-- 换图区域 -->
    <div class="change-img">
      <img :src="url" mode="aspectFill" alt="">
      <button @click="change">选图</button>
    </div>
    <!-- 生成海报按钮-->
    <button v-on:click="createPoster" class="create-poster-btn">创建海报</button>

    <!-- 海报弹窗 -->
    <div class="poster-modal" v-if="isShowPoster">
      <div class="shade">
        <div class="poster-box"> 
          <img mode="widthFix" :src="posterUrl" alt="">
          <i class="del-poster" @click="delPoster">X</i>
          <button class="save-poster" @click="savePoster">保存</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {

    data() {
      return {
        // 选择图片的url
        url: "",
        // 海报图片
        posterUrl: "",
        // 是否显示海报
        isShowPoster: false,

      }
    },
    methods: {
      /**
       * 选择图片
       * @method change
       */
      change() {
        let _this = this;
        // 调用选择图片或者使用拍照api
        wx.chooseImage({
          count: 1,
          sizeType: ['original', 'compressed'],
          sourceType: ['album', 'camera'],
          success: function (res) {
            // 图片的本地临时路径
            let tempFilePaths = res.tempFilePaths;
            _this.url = tempFilePaths[0];
            wx.showToast({
              title: '更换成功',
              icon: 'none',
            });
          }
        })
      },
      /**
       * 创建海报
       * @method createPoster
       */
      createPoster() {
        // 当没有图片的时候
        if (this.url == "") {
          wx.showToast({
            title: '请选择图片',
            icon: 'none',
          });
          return;
        }
        // 获取canvas上下文对象
        let canvasContext = wx.createCanvasContext("canvas", this);
        // 绘制顶部图片
        canvasContext.drawImage(this.url, 87.5, 0, 200, 111);
        // 绘制(中山宴会)文字
        canvasContext.setFontSize(20);
        canvasContext.setFillStyle("yellow");
        canvasContext.setTextAlign("center");
        canvasContext.fillText("中山宴会", 187.5, 130);
        // 把绘制的内容画到canvas上面去
        canvasContext.draw(false, () => {
          // 把画布里的内容生成一张图片
          wx.canvasToTempFilePath({
            x: 0,
            y: 0,
            width: 375,
            height: 200,
            canvasId: "canvas",
            fileType: "jpg",
            quality: 1.0,
            success: (res) => {
              this.posterUrl = res.tempFilePath // 海报图片
              this.isShowPoster=true;
            },
            fail: () => {},
          }, this);

        });

      },
      /**
       * 删除海报
       * @method delPoster
       */
       delPoster(){
        // 把对应的海报隐藏
        this.isShowPoster=false;
       },
       /**
        * 保存海报
        * @method savePoster
        */
       savePoster(){
         wx.authorize({
           scope: 'scope.writePhotosAlbum',
           success: (result) => {
             console.log(this.posterUrl);
              wx.saveImageToPhotosAlbum({
                filePath:this.posterUrl,
                success: (result) => {
                  wx.showToast({
                    title: '保存到相册中',
                    icon: 'success'
                  })
                },
                fail: () => {
                  wx.showToast({
                    title: '保存失败',
                    icon: 'error'
                  })
                },
              });
           },
           fail: () => {
             console.log("保存失败");
             wx.openSetting({
               success: (result) => {
                 
               },
               fail: () => {},
             });
               
           },
         });
           
       }
      
    }

  }

</script>

<style scoped>
  /* 画板内容 */
  .canvas-con{
    position: fixed;
    top: 0;
    left: 0;

  }
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


  /* 海报弹窗 */
  .poster-modal .shade {
    position: fixed;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.3);
  }
  .poster-modal .poster-box{
    width: 100%;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    
  }
  .poster-modal img {
    width: 100%;
    border: 2rpx solid #ccc;
    border-radius: 20rpx;
  }
  .poster-modal .del-poster{
    width: 50rpx;
    height: 50rpx;
    border-radius: 50%;
    text-align: center;
    display: block;
    line-height: 50rpx;
    border: 2rpx solid #ccc;
    color: #ccc;
    position: absolute;
    right: 0;
    top: 0;
  }
  .poster-modal  .save-poster{
    padding: 0;
    width: 150rpx;
    height: 50rpx;
    line-height: 50rpx;
    text-align: center;
    position: absolute;
    left: 50%;
    bottom: 50rpx;
    transform: translateX(-50%);
  }
</style>
