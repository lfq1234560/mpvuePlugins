<template>
  <div class="cropper">
    <!-- 放图片区域 -->
    <div class="img-box" @click="chooseImg">
      <img :src="imgUrl" mode="aspectFill" alt="">
      <p>选择图片</p>
    </div>
    <!--  裁剪组件 -->
    <div class="cropper-box" v-show="isShowCropper">
      <mpvue-cropper ref="cropper" :option="cropperOption"></mpvue-cropper>
      <div class="btn">
        <button @click="chooseImg">重新选择照片</button>
        <button @click="getCropperImage">使用这张照片</button>
      </div>
    </div>
  </div>
</template>

<script>
  import mpvueCropper from "mpvue-cropper";
  // 获取手机屏幕信息
  const device = wx.getSystemInfoSync();
  const width = device.windowWidth;
  const height = device.windowHeight;
  export default {
    data() {
      return {
        // 裁剪组件的配置参数
        cropperOption: {
          id: "cropper", //手势操作的canvas组件标识符
          targetId: "targetCropper", //生成截图的canvas组件标识符
          pixelRatio: device.pixelRatio, //设备的像素比
          width: width, //画布的宽度
          height: height - 50, //画布的高度
          scale: 0.5, //最大缩放倍数
          zoom: 8, //缩放系数
          cut: {
            x: (width - 344) / 2, //裁剪区域水平方向的起始位置
            y: (height - 370) / 2, //裁剪曲云竖直方向的起始位置
            width: 344, //裁剪区域的宽度
            height: 370, //裁剪区域的高度
          },
          boundStyle: { //裁剪区域的样式
            color: "red", //四个角的颜色
            mask: "rgba(0,0,0,0.8)", //遮罩的颜色
            lineWidth: 1 //四个角的宽度
          },
        },
        // 是否显示
        isShowCropper: false,
        // 图片路径
        imgUrl: ""

      }
    },
    components: {
      // 裁剪组件
      mpvueCropper
    },
    methods: {
      /**
       * 选择图片
       * @method chooseImg
       */
      chooseImg() {
        let _this = this;
        wx.chooseImage({ // 重手机相册选择图片或者拍照
          count: 1,
          sizeType: ['original', 'compressed'],
          sourceType: ['album', 'camera'],
          success: function (res) {
            var tempFilePaths = res.tempFilePaths[0];
            _this.isShowCropper = true;
            _this.$refs.cropper.pushOrigin(tempFilePaths); // 把图片路径添加到裁剪组件里面去

          }
        })
      },
      /**
       * 得到裁剪图片
       * @method getCropperImage
       */
      getCropperImage() {
        this.$refs.cropper.getCropperImage({
            original: false,
            fileType: "png",
          })
          .then((src) => {
            this.imgUrl = src; // 裁剪之后的图片地址
            this.isShowCropper = false;
          })
          .catch((e) => {
            wx.showToast({
              title: '裁剪失败',
              icon: 'none',
            });
          });
      }
    }
  }

</script>

<style scoped>
  /* 放图片区域 */
  .img-box {
    height: 300rpx;
    position: relative;
    z-index: 3;
  }

  .img-box img {
    margin: 0 auto;
    display: block;
    border: 2rpx solid red;
    border-radius: 10rpx;
    height: 100%;
    width: 50%;
    z-index: 1;
    position: relative;

  }

  .img-box p {
    width: 100%;
    text-align: center;
    position: absolute;
    left: 0;
    top: 50%;
    transform: translateY(-50%);
    font-size: 30rpx;
    color: red;
    z-index: 2;
  }

  /* 裁剪组件  */
  .cropper-box {
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 4;
    background: #fff;
  }

  .btn {
    height: 50px;
    width: 100%;
    position: absolute;
    bottom: 0;
    display: flex;
  }

  .btn button {
    padding: 0;
    height: 100%;
    width: 50%;
    line-height: 50px;
    font-size: 28rpx;
    color: #000;
    text-align: center;

  }

  .btn button::after {
    border: 0;
  }

</style>
