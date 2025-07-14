<template>
  <view>
    
  </view>
</template>

<script>
  export default {
    data() {
      return {
        //轮播图的数据列表
        swiperList: [],
      };
    },
    onLoad() {
      //在小程序页面刚加载的时候，调用获取轮播图数据的方法
      this.getSwiperList()
    },
    methods:{
      //获取轮播图数据的方法
      async getSwiperList(){
        //发起请求
        const {data: res} = await uni.$http.get('/api/public/v1/home/swiperdata')
        //请求失败
        if(res.meta.status !== 200){
          return uni.showToast({
            title:'数据请求失败！',
            duration: 1500,
            icon: 'none'
          })
        }
        //请求成功,为data中的数据赋值
        this.swiperList = res.message
      }
    }
  }
</script>

<style lang="scss">

</style>
