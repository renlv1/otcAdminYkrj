<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb detail-page">
      <div class="title-left">
        <router-link to="/user/backTransferLog" class="title-m">后台转账日志 </router-link>
        <span> / 日志详情</span>
      </div>
    </div>
    <div class="page-wrap detail-cont">
      <ul class="ul-w">
        <li class="li-item">
          <div class="li-left">ID</div>
          <div class="li-right">{{data.id}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">用户名</div>
          <div class="li-right">{{data.userName}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">用户地址</div>
          <div class="li-right">{{data.fromAddress}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">收款地址</div>
          <div class="li-right">{{data.toAddress}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">转账金额</div>
          <div class="li-right">{{data.amount}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">手续费</div>
          <div class="li-right">{{data.serviceCharge}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">gasPrice</div>
          <div class="li-right">{{data.gasPrice}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">gasLimit</div>
          <div class="li-right">{{data.gasLimit}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">hash</div>
          <div class="li-right">{{data.hash}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">转账调用次数</div>
          <div class="li-right">{{data.transferCount}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">转账状态</div>
          <div class="li-right">{{$transferState(data.transferState)}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">创建时间</div>
          <div class="li-right">{{data.createAt | time}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">修改时间</div>
          <div class="li-right">{{data.updateAt | time}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">备注</div>
          <div class="li-right">{{data.remarks || '-'}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">备用字段1</div>
          <div class="li-right">{{data.backup1 || '-'}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">备用字段2</div>
          <div class="li-right">{{data.backup2 || '-'}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">备用字段3</div>
          <div class="li-right">{{data.backup3 || '-'}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">币种</div>
          <div class="li-right">{{data.currency}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">转账类型</div>
          <div class="li-right">{{data.transferType === 1 ? '汇总转账' : '手续费转账'}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">关联id</div>
          <div class="li-right">{{data.relateId || '-'}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">汇总时账户待汇总余额</div>
          <div class="li-right">{{data.accountBalance || '-'}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">汇总时ETH钱包账户余额</div>
          <div class="li-right">{{data.ethBalance || '-'}}</div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return{
        id: this.$route.query.id,
        data: ''
      }
    },
    created() {
      this.getData()
    },
    methods: {
      getData() {
        this.$fetch.post(`/user/managerTransferAccountLog/queryTransferAccountLog?id=${this.id}`).then(res => {
          if(res.code === 0) {
            this.data = res.data.resultData
          }
        })
      }
    }
  }
</script>

<style scoped>
.zoom-enter-to{
  transition: none;
}
</style>
