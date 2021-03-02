<template>
  <div class="copy">
    <p>{{url}}</p>
    <button @click="copy">点我复制</button> 
  </div>
</template>

<script>

export default {

  data () {
    return {
      //百度网址
      url:"http://baidu.com",
    }
  },
  methods:{
    /**
     * description:复制功能
     */
    copy(){
      //调用设置剪切板api
      wx.setClipboardData({
        data: this.url,
        success: (result) => {
          
        },
        fail: () => {},
        complete: () => {}
      });
        
    }
  }

}
</script>

<style scoped>
p{
  height: 50rpx;
  line-height: 50rpx;
  text-align: center;
  background:blue;
}

</style>
