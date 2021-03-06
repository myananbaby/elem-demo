<template>
  <div class="seller" v-el:seller>
    <div class="seller-content">
      <div class="overview">
        <div class="overview-top">
          <h1 class="title">{{seller.name}}</h1>
          <div class="desc border-1px">
            <star :size="36" :score="seller.score"></star>
            <span class="text">({{seller.ratingCount}})</span>
            <span class="text">月售{{seller.sellCount}}单</span>
          </div>
        </div>
        <div class="overview-bottom">
          <ul class="remark">
            <li class="block">
              <h2>起送价</h2>
              <div class="content">
                <span class="stress">{{seller.minPrice}}</span>元
              </div>
            </li>
            <li class="block">
              <h2>商家配送</h2>
              <div class="content">
                <span class="stress">{{seller.deliveryPrice}}</span>元
              </div>
            </li>
            <li class="block">
              <h2>平均配送时间</h2>
              <div class="content">
                <span class="stress">{{seller.deliveryTime}}</span>元
              </div>
            </li>
          </ul>
        </div>
        <div class="favorite" @click="toggleFavorite">
          <span class="icon-favorite" :class="{'active':favorite}"></span>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="content">{{seller.bulletin}}</div>
        <ul class="support-wrapper" v-if="seller.supports">
          <li class="support-item" v-for="support in seller.supports">
            <span class="icon" :class="classMap[support.type]"></span>
            <span class="text">{{support.description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" v-el:pic-wrapper>
          <ul class="pic-list" v-el:pic-list>
             <li class="pic-item" v-for="item in seller.pics">
               <img :src="item" width="120" height="90">
             </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="info">
        <h1 class="title">商家信息</h1>
        <ul>
          <li class="info-item" v-for="item in seller.infos">{{item}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import split from 'components/split/split';
  import star from 'components/star/star';
  import BScroll from 'better-scroll';
  import {saveToLocal, loadFromLocal} from 'common/js/store';

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        favorite: (() => {
          return loadFromLocal(this.seller.id, 'favorite', false);
        })()
      };
    },
    computed: {
      favoriteText() {
        return this.favorite ? '已收藏' : '收藏';
      }
    },
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    watch: {
      'seller'() {
        this._initScroll();
        this._initPics();
      }
    },
    ready() {
      this._initScroll();
      this._initPics();
    },
    methods: {
      toggleFavorite(event) {
        if (!event._constructed) {
          return false;
        }
        console.log(this.favorite);
        this.favorite = !this.favorite;
        saveToLocal(this.seller.id, 'favorite', this.favorite);
      },
      _initScroll() {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$els.seller, {
            click: true
          });
        } else {
          this.scroll.refresh();
        }
      },
      _initPics() {
        if (this.seller.pics) {
          let picWidth = 120;
          let margin = 6;
          let width = (picWidth + margin) * this.seller.pics.length - margin;
          this.$els.picList.style.width = width + 'px';
          if (!this.picsScroll) {
            this.picsScroll = new BScroll(this.$els.picWrapper, {
              scrollX: true,
              eventPassthrough: 'vertical'
            });
          } else {
            this.picsScroll.refresh();
          }
        }
      }
    },
    components: {
      split,
      star
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";

  .seller
    position: absolute
    left: 0
    top: 174px
    bottom: 0
    width: 100%
    overflow: hidden
    .overview
      position: relative
      .overview-top
        padding: 18px
        border-1px(rgba(7, 17, 27, .1))
        .title
          margin-bottom: 8px
          line-height: 14px
          color: rgb(7, 17, 27)
          font-size: 14px
        .desc
          font-size: 0
          .star
            display: inline-block
            margin-right: 8px
            vertical-align: top
          .text
            display: inline-block
            vertical-align: top
            margin-right: 12px
            line-height: 18px
            font-size: 10px
            color: rgb(77, 85, 93)
      .overview-bottom
        padding: 18px 0
        .remark
          display: flex
          .block
            flex: 1
            text-align: center
            border-right: 1px solid rgba(7, 17, 27, .1)
            &:last-child
              border: none
            h2
              margin-bottom: 4px
              line-height: 10px
              font-size: 10px
              color: rgb(147, 153, 159)
            .content
              line-height: 24px
              font-size: 10px
              color: rgb(7, 17, 27)
              .stress
                font-size: 24px
      .favorite
        position: absolute
        width: 50px
        right: 12px
        top: 18px
        text-align: center
        .icon-favorite
          display: block
          margin-bottom: 4px
          line-height: 24px
          font-size: 24px
          color: #d4d6d9
          &.active
            color: rgb(240, 20, 20)
        .text
          line-height: 10px
          font-size: 10px
          color: rgb(77, 85, 93)
    .bulletin
      padding: 18px 18px 0 18px
      .title
        margin-bottom: 8px
        line-height: 14px
        color: rgb(7, 17, 27)
        font-size: 14px
      .content
        padding: 0 12px 16px 12px
        border-1px(rgba(7, 17, 27, .1))
        line-height: 24px
        font-size: 12px
        color: rgb(240, 20, 20)
      .support-wrapper
        .support-item
          padding: 16px 12px
          border-1px(rgba(7, 17, 27, .1))
          font-size: 0
          &:last-child
            border-none()
          .icon
            display: inline-block
            width: 16px
            height: 16px
            vertical-align: top
            margin-right: 6px
            background-size: 16px 16px
            background-repeat: no-repeat
            &.decrease
              bg-image('decrease_4')
            &.discount
              bg-image('discount_4')
            &.guarantee
              bg-image('guarantee_4')
            &.invoice
              bg-image('invoice_4')
            &.special
              bg-image('special_4')
          .text
            line-height: 16px
            font-size: 12px
            color: rgb(7, 17, 27)
    .pics
      padding: 18px
      .title
        margin-bottom: 12px
        line-height: 14px
        color: rgb(7, 17, 27)
        font-size: 14px
      .pic-wrapper
        width: 100%
        overflow: hidden
        white-space: nowrap
        .pic-list
          font-size: 0
          .pic-item
            display: inline-block
            margin-right: 6px
            width: 120px
            height: 90px
            &:last-child
              margin: 0
    .info
      padding: 18px
      .title
        margin-bottom: 12px
        line-height: 14px
        color: rgb(7, 17, 27)
        font-size: 14px
      .info-item
        padding: 16px 12px
        line-height: 16px
        border-1px(rgba(7, 17, 27, .1))
        font-size: 12px
        &:last-child
          border-none()
</style>















