<template>
  <div>
    <!-- 顶部滑动条区域 -->
    <div id="slider" class="mui-slider">
      <div id="sliderSegmentedControl"
           class="mui-scroll-wrapper mui-slider-indicator mui-segmented-control mui-segmented-control-inverted">
        <div class="mui-scroll">
          <a :class="['mui-control-item', item.id === 0 ? 'mui-active' : '']" v-for="item in cates" :key="item.id" @click="getPhotoListByCateId(item.id)">
            {{ item.title }}
          </a>
        </div>
      </div>
    </div>

    <!--图片列表-->
    <ul class="photo-list">
      <router-link v-for="item in list" :key="item.id" :to="'/home/photoinfo/'+item.id" tag="li">
        <img v-lazy="item.img_url">
        <div class="info">
          <h1 class="info-title">
          {{ item.title }}
          </h1>
          <div class="info-body"> {{ item.zhaiyao }}</div>
        </div>
      </router-link>
    </ul>

  </div>
</template>

<script>
  import mui from '../../lib/mui/js/mui.min.js';

  export default {
    data() {
      return {
        cates: [],
        list: [],
      };
    },
    mounted() {   //页面中加载完DOM元素时
      mui(".mui-scroll-wrapper").scroll({
        deceleration: 0.0005 //flick 减速系数，系数越大，滚动速度越慢，滚动距离越小，默认值0.0006
      });
    },
    created() {
      this.getAllCategory();
      this.getPhotoListByCateId(0);
    },
    methods: {
      //获取所有分类图片
      getAllCategory() {
        this.$http.get('api/getimgcategory').then(res => {
          if (res.body.status === 0) {
            res.body.message.unshift({title: '全部', id: 0});
            this.cates = res.body.message;
          }
        });
      },
      getPhotoListByCateId(cateId) {
        // 根据 分类Id，获取图片列表
        this.$http.get("api/getimages/" + cateId).then(result => {
          if (result.body.status === 0) {
            this.list = result.body.message;
          }
        });
      }
    },
  };

</script>

<style lang="less" scoped>
  * {
    touch-action: pan-y;
  }
  .photo-list{
    margin: 0;
    padding: 10px 10px 0 10px;
    li{
      position: relative;
      margin-bottom: 10px;
      text-align: center;
      list-style: none;
      background-color: #ccc;
      box-shadow: 0 0 9px #999;
      img{
        width: 100%;
        display: block;
      }
      img[lazy="loading"] {
        width: 40px;
        height: 300px;
        margin: auto;
      }
      .info {
        position: absolute;
        color: white;
        text-align: left;
        bottom: 0;
        background-color: rgba(0, 0, 0, 0.4);
        max-height: 84px;
        .info-title {
          font-size: 14px;
        }
        .info-body {
          font-size: 13px;
        }
      }
    }
  }

</style>