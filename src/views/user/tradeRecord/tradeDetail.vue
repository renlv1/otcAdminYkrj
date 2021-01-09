<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb detail-page">
      <div class="title-left">
        <router-link to="/user/tradeRecord" class="title-m">交易记录 </router-link>
        <span> / 记录详情</span>
      </div>
    </div>
    <div class="page-wrap detail-cont">
      <ul class="ul-w">
        <li class="li-item">
          <div class="li-left">ID</div>
          <div class="li-right">{{data.id}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">发送者用户名</div>
          <div class="li-right">{{isEmptyText(obj.sendUserName)}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">发送者用户地址</div>
          <div class="li-right">{{data.sendAddress}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">接收者用户名</div>
          <div class="li-right">{{isEmptyText(obj.receiveUserName)}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">接收者用户地址</div>
          <div class="li-right">{{data.receiveAddress}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">币种</div>
          <div class="li-right">{{data.currency}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">金额</div>
          <div class="li-right">{{data.amount}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">手续费</div>
          <div class="li-right">{{data.fee  || '-'}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">hash</div>
          <div class="li-right">{{isEmptyText(data.hash)}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">状态</div>
          <div class="li-right">{{$stateType(data.state)}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">支付类型</div>
          <div class="li-right">{{$paymentType(data.paymentType)}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">创建时间</div>
          <div class="li-right">{{data.createAt}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">修改时间</div>
          <div class="li-right">{{isEmptyText(data.updateAt)}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">orderId</div>
          <div class="li-right">{{data.orderId || '-'}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">订单类型</div>
          <div class="li-right">{{$orderType(data.orderType)}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">回调次数</div>
          <div class="li-right">{{isEmptyText(data.callbackTime)}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">回调状态</div>
          <div class="li-right">{{$callbackState(data.callbackState)}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">下次回调时间</div>
          <div class="li-right">{{isEmptyText(data.nextCallBackDate)}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">订单ID</div>
          <div class="li-right">{{data.dingdanid}}</div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
  // import Dialog from '@/components/dialog/dialog'
  export default {
    name: "transferLogDetail",
    data() {
      return{
        id: this.$route.query.id,
        obj: JSON.parse(this.$route.query.obj),
        data: '',
      }
    },
    created() {
      this.getData()
    },
    methods: {
      getData() {
        this.$fetch.post(`/user/transaction/preCatTransatinoDta?id=${this.id}`).then(res => {
          if(res.code === 0) {
            this.data = res.data.resultData
          }
        })
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
  .dialog-input{
    width 240px!important
  }
  .li-item{
    line-height: 2
  }
  .ul-w{
      padding-bottom: 0
  }
  .problem-box{
    display flex
    justify-content space-between
    align-items center
    .dropdown-wrap {
      cursor pointer
      text-align center
      line-height 40px
      width: 250px;
      margin-right: 20px;
      background-color #fff
      border: 1px solid #DCDFE6;
      border-radius: 6px;
      padding: 0 10px;
      font-size 15px
      height: 40px;
      >>>.el-dropdown-link{
        display flex
        justify-content space-between
        align-items center
      }
    }
    >>>.el-dropdown-menu{
      width 240px!important
    }
  }
  >>>.el-dialog__body{
    padding 30px 20px!important
  }

  .zoom-enter-to{
    transition: none;
  }
</style>
