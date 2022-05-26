<template>
  <view>
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true" >
      <swiper-item v-for="(item,i) in swiperList" :key='i'>  
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
          <!-- 动态绑定图片的src属性 -->
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>
    <!-- 分类导航区域 -->
    <view class="catitems">
    <view v-for="(item,i) in Catitems" :key="i" class="catitems-item"  @click="navClickHandler(item)">
      <image :src="item.image_src" class="catitems-img"></image>
     <!-- <text class="catitems-text">{{item.name}}</text> -->
    </view>
    </view>
    
    <!-- 楼层区域 -->
    <view class="floor">
      <view class="floor-item" v-for="(item,i) in Floor" :key="i">
        <!-- 楼层标题 -->
        <image :src="item.floor_title.image_src" class="floor_title"></image>
        <!-- 楼层图片区域 -->
        <view class="floor-box">
          <!-- 左侧大图 -->
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}" mode="widthFix" ></image>
          </navigator>
          <!-- 右侧小图 -->
          <view class="right-img-box">
            <navigator class="right-img-item" v-for="(item2,i2) in item.product_list" :key="i2" v-if = "i2 !== 0"  :url="item2.url">
              <image :src="item2.image_src" mode="widthFix" :style="{width: item2.image_width+'rpx'}"></image>
            </navigator>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        //轮播图的数据列表
        swiperList: [],
        //分类导航数据列表
        Catitems:[],
        //楼层信息
        Floor:[]
      };
    },
    onLoad() {
      //在小程序页面刚加载的时候，调用获取轮播图数据的方法
      this.getSwiperList(),
      this.getCatitems(),
      this.getFloor()
    },
    methods:{
      //获取轮播图数据的方法
      async getSwiperList(){
        //发起请求
        const {data: res} = await uni.$http.get('/api/public/v1/home/swiperdata')
        //请求失败
        if(res.meta.status !== 200){
          return uni.$showMsg()
        }
        //请求成功,为data中的数据赋值
        this.swiperList = res.message
      },
      //获取分类导航区域的方法
      async getCatitems(){
        const {data:res} = await uni.$http.get('/api/public/v1/home/catitems')
        //请求失败
        if(res.meta.status !== 200){
          return uni.$showMsg()
        }
        this.Catitems = res.message
      },
      navClickHandler(item){
        if(item.name === '分类'){
          uni.switchTab({
            url:"/pages/cate/cate"
          })
        }
      },
      //请求楼层数据函数
     async getFloor(){
      const {data: res}  = await uni.$http.get('/api/public/v1/home/floordata')
       if(res.meta.status !== 200)  return uni.$showMsg()
       //通过双层forEach循环，处理url地址
       res.message.forEach(floor => {
         floor.product_list.forEach(prod => {
           prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
         })
       })
       this.Floor = res.message
     }
   
    }
  }
</script>

<style lang="scss">
swiper {
 height: 330rpx;

 .swiper-item,
 image {
   width: 100%;
   height: 100%;
 }
}
.catitems{
  display: flex;
  justify-content: space-around;
  margin: 15px 0;
  .catitems-img{
    width: 128rpx;
    height: 140rpx;
  }
}
.floor_title{
   height: 60rpx;
    width: 100%;
  display: flex;
}
.floor-box{
  display: flex;
  padding-left: 10rpx;
}
 .right-img-box{
   display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }
</style>