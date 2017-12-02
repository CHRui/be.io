<template>
  <div class="tmpl">
    <!-- 导航栏 -->
    <div class="section">
      <div class="location">
        <span>当前位置：</span>
        <a href="/index.html">首页</a> &gt;
        <a href="/goods.html">购物商城</a>
        <a href="/goods/42/1.html">商品详情</a>
    
      </div>
    </div>

    <!-- 商品详情 -->
    <div class="section" v-if="info.goodsinfo">
      <div class="wrapper clearfix">
        <div class="wrap-box">
          <!--页面左边-->
          <div class="left-925">
            <div class="goods-box clearfix">
              <!--商品图片-->
              <div class="pic-box">
                <div class="magnifier" id="magnifier1">
                  <div class="magnifier-container">
                    <div class="images-cover"></div>
                    <!--当前图片显示容器-->
                    <div class="move-view"></div>
                    <!--跟随鼠标移动的盒子-->
                  </div>
                  <div class="magnifier-assembly">
                    <div class="magnifier-btn">
                      <span class="magnifier-btn-left">&lt;</span>
                      <span class="magnifier-btn-right">&gt;</span>
                    </div>
                    <!--按钮组-->
                    <div class="magnifier-line">
                      <ul class="clearfix animation03">
                        <li v-for="item in info.imglist" :key="item.id">
                          <div class="small-img">
                            <img :src="item.original_path" />
                          </div>
                        </li>
    
                      </ul>
                    </div>
                    <!--缩略图-->
                  </div>
                  <div class="magnifier-view"></div>
                  <!--经过放大的图片显示容器-->
                </div>
              </div>
              <!--/商品图片-->
    
              <!--商品信息-->
              <div class="goods-spec">
                <h1 v-text="info.goodsinfo.title"></h1>
                <p class="subtitle" v-text="info.goodsinfo.sub_title"></p>
                <div class="spec-box">
                  <dl>
                    <dt>货号</dt>
                    <dd id="commodityGoodsNo">{{info.goodsinfo.goods_no}}</dd>
                  </dl>
                  <dl>
                    <dt>市场价</dt>
                    <dd>
                      <s id="commodityMarketPrice">¥{{info.goodsinfo.market_price}}</s>
                    </dd>
                  </dl>
                  <dl>
                    <dt>销售价</dt>
                    <dd>
                      <em class="price" id="commoditySellPrice">¥{{info.goodsinfo.sell_price}}</em>
                    </dd>
                  </dl>
                </div>
    
                <div class="spec-box">
                  <dl>
                    <dt>购买数量</dt>
                    <dd>
                      <el-input-number size="small" v-model="num1" @change="buyCount" :min="1" :max="info.goodsinfo.stock_quantity">
    
                      </el-input-number>
                      <span class="stock-txt">
                        库存
                        <em id="commodityStockNum">{{info.goodsinfo.stock_quantity}}</em>件 &nbsp;
                      </span>
                    </dd>
                  </dl>
                  <dl>
                    <dd>
                      <div class="btn-buy" id="buyButton">
                        <button class="buy">立即购买</button>
                        <button ref="btnaddcar" class="add" @click="addCar">加入购物车</button>
                      </div>
                    </dd>
                  </dl>
                </div>
    
              </div>
              <!--/商品信息-->
            </div>
    
            <div id="goodsTabs" class="goods-tab bg-wrap">
              <!--选项卡-->
              <Affix>
                <div id="tabHead" class="tab-head" style="position: static; top: 517px; width: 925px;">
                  <ul>
                    <li @click="istabShow = true">
                      <a v-bind="{class:(istabShow?'selected':'')}" href="javascript:;">商品介绍</a>
                    </li>
                    <li @click="istabShow = false">
                      <a v-bind="{class:(!istabShow?'selected':'')}" href="javascript:;" class="">商品评论</a>
                    </li>
                  </ul>
                </div>
              </Affix>
              <!--/选项卡-->
    
              <!--选项内容-->
              <div class="tab-content entry" v-show="istabShow">
                <div style="padding:5px" v-html="info.goodsinfo.content"></div>
              </div>
    
              <!--网友评论-->
              <div class="tab-content" v-show="!istabShow">
    
                <div class="comment-box">
                  <!--取得评论总数-->
                  <form id="commentForm" name="commentForm" class="form-box">
                    <div class="avatar-box">
                      <i class="iconfont icon-user-full"></i>
                    </div>
                    <div class="conn-box">
                      <div class="editor">
                        <textarea id="txtContent" name="txtContent" sucmsg=" " v-model="commentTxt" datatype="*10-1000" nullmsg="请填写评论内容！"></textarea>
                        <span class="Validform_checktip"></span>
                      </div>
                      <div class="subcon">
                        <input id="btnSubmit" name="submit" type="button" @click="postComment" value="提交评论" class="submit">
                        <span class="Validform_checktip"></span>
                      </div>
                    </div>
                  </form>
                  <!-- 评论列表区域 -->
                  <ul id="commentList" class="list-box">
                    <p v-show="commentList.length<=0" style="margin:5px 0 15px 69px;line-height:42px;text-align:center;border:1px solid #f7f7f7;">暂无评论，快来抢沙发吧！</p>
                    <li v-for="item in commentList" :key="item.id">
                      <div class="avatar-box">
                        <i class="iconfont icon-user-full"></i>
                      </div>
                      <div class="inner-box">
                        <div class="info">
                          <span>匿名用户</span>
                          <span>{{item.add_time | datefmt('YYYY-MM-DD HH:mm:ss')}}</span>
                        </div>
                        <p>{{item.content}}</p>
                      </div>
                    </li>
    
                  </ul>
                  <!--放置页码-->
                  <div class="page-box" style="margin:5px 0 0 62px">
                    <!-- 使用elementUI的分页组件替代 -->
                    <el-pagination @size-change="sizeChange" @current-change="pageChange" :current-page="pageIndex" :page-sizes="[2,5,10,20,30]"
                      :page-size="pageSize" layout="total, sizes, prev, pager, next, jumper" :total="totalcount">
                    </el-pagination>
                  </div>
                  <!--/放置页码-->
                </div>
                <!--/网友评论-->
              </div>
    
            </div>
    
          </div>
          <!--/页面左边-->
    
          <!--页面右边-->
          <div class="left-220">
            <div class="bg-wrap nobg">
              <div class="sidebar-box">
                <h4>推荐商品</h4>
                <ul class="side-img-list">
    
                  <li v-for="item in info.hotgoodslist" :key="item.id">
                    <div class="img-box">
                      <router-link v-bind="{to:'/site/goodsinfo/'+item.id}">
                        <img :src="item.img_url">
                      </router-link>
                    </div>
                    <div class="txt-box">
                      <router-link v-bind="{to:'/site/goodsinfo/'+item.id}">
                        {{item.title}}
                      </router-link>
                      <span>{{item.add_time | datefmt}}</span>
                    </div>
                  </li>
                </ul>
              </div>
            </div>
          </div>
          <!--/页面右边-->
        </div>
      </div>
    </div>

    <!-- 定义一个div是用来展示图片的 -->
    <transition @before-enter="benter" @enter="enter" @after-enter="aenter">
      <div v-if="info.imglist" class="img" ref="img" v-show="isshow">
        <img width="50" hieght="50" :src="info.imglist[0].thumb_path" alt="">
      </div>
    </transition>
  </div>
</template>
<script>
  import '../../../statics/jqplugins/jqimgzoom/js/magnifier.js';
  // 导入事件总线的相关数据
  import {vm,KEY} from '../../kits/bus.js';
  import {setItem} from '../../kits/localSto.js';
  export default {
    data(){
      return{
        offsetObj:{},
        lbcOffSet:{},
        isshow: false,
        currentBuyCount: 1,
        commentTxt: '',
        pageIndex: 1,
        pageSize: 2,
        totalcount: 0,
        commentList: [],
        istabShow: true,
        num1: 1,
        info: {}
      }
    },
    mounted() {
      this.getinfo();
      this.getCommentList();
      setTimeout(()=>{
        var offsetObj = $(this.$refs.btnaddcar).offset();
        this.offsetObj = offsetObj;
        $(this.$refs.img).css("left",offsetObj.left).css("top",offsetObj.top);
        var lbcOffSet = $("#layoutbuycar").offset();
        this.lbcOffSet = lbcOffSet;
      },200);
    },
    watch:{
      '$route': function(){
        this.getinfo();
      }
    },
    methods:{
      benter(el){
        el.style.left = this.offsetObj.left + 'px';
        el.style.top = this.offsetObj.top + 'px';
      },
      enter(el,done){  
        el.offsetWidth;
        el.style.left = this.lbcOffSet.left + 'px';
        el.style.top = this.lbcOffSet.top + 'px';
        done();
      },
      aenter(el){
        this.isshow = false;
      },
      addCar(){
        // vm.$emit(KEY,this.currentBuyCount);
        // var goodsobj = {gid:this.$route.params.goodsid,count:this.currentBuyCount};
        var goodsobj = {gid:this.$route.params.goodsid, count:this.currentBuyCount};
        setItem(goodsobj);
        this.$store.dispatch('changeCount',goodsobj);
        this.isshow  = true;
      },
      // 定义提交评论方法
      postComment(){
        var url ='/site/validate/comment/post/goods/'+this.$route.params.goodsid;
        this.$ajax.post(url,{commenttxt:this.commentTxt}).then(res=>{
          if(res.data.status == 1){
            this.$message.error(res.data.message);
            return;
          }
          // 提交成功后刷新
          this.getCommentList();
          // 清空文本框
          this.commentTxt = '';
        });
      },
      // 定义分页
      sizeChange(size){
        this.pageSize = size;
        this.getCommentList();
      },
      pageChange(index){
        this.pageIndex = index;
        this.getCommentList();
      },
      getCommentList(){
        var url = `/site/comment/getbypage/goods/${this.$route.params.goodsid}?pageIndex=${this.pageIndex}&pageSize=${this.pageSize}`;
        this.$ajax.get(url).then(res=>{
          this.commentList = res.data.message;
          this.totalcount = res.data.totalcount;
        });
      },
      buyCount(val){
        console.log(val);
        this.currentBuyCount = val;
      },
      getinfo(){
        this.$ajax.get('/site/goods/getgoodsinfo/'+this.$route.params.goodsid).then(res=>{
          this.info = res.data.message;
          if(!this.info.goodsinfo){
            this.$message.error('传入的商品ID非法');
          }
          setTimeout(function() {
            $('#magnifier1').imgzoon({magnifier:'#magnifier1'});
          }, 200);
        })
      }
    }
  }
          
</script>
<style scoped>
  @import url('../../../statics/jqplugins/jqimgzoom/css/magnifier.css');
  .img{
    border: 1px solid red;
    width: 50px;
    height: 50px;
    position: absolute;
    transition: all 1s;
  }
</style>