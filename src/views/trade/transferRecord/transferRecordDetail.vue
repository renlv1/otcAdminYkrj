<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb detail-page">
      <div class="title-left">
        <router-link to="/trade/transferRecord" class="title-m">转账记录 </router-link>
        <span> / 转账详情</span>
      </div>
      <div class="btn-right">
<!--        v-show="dataDetail.orderStatus == 18 && (dataDetail.recordType == 1 || dataDetail.recordType == 8)"-->
        <span class="pay-money" @click="$router.push({path: '/trade/transferRecord/transferAudit',query: {id: id}})" v-show="dataDetail.orderStatus == 18 ">审核</span>
        <span class="pay-money" @click="$router.push({path: '/trade/transferRecord/intoAudit',query: {id: id}})" v-show="dataDetail.orderStatus == 18 && (dataDetail.recordType == 1 || dataDetail.recordType == 8)">转入审核</span>
        <span class="pay-money" @click="modiftyBtn()" v-show="dataDetail.checkCount>=6 && dataDetail.orderStatus==12 && dataDetail.recordType ==2">重新转账</span>
        <span class="btn-save" @click="saveBtn()">保存</span>
      </div>
    </div>
    <div class="page-wrap detail-cont">
      <ul class="ul-w ">
        <li class="li-item search">
          <div class="search-box">
            <div class="label">ID</div>
            <div class="input-wrap">{{dataDetail.id}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">发送者用户名</div>
            <div class="input-wrap">{{dataDetail.sendUserName}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">发送者用户地址</div>
            <div class="input-wrap">{{dataDetail.sendUserAddress  || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">接收者</div>
            <div class="input-wrap">{{dataDetail.receiveUserName || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">接收者地址</div>
            <div class="input-wrap">{{dataDetail.receiveUserAddress || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">接收者以太坊钱包地址</div>
            <div class="input-wrap">{{dataDetail.receiveWalletAddress || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">数据类型</div>
            <div class="input-wrap">{{$recordType(dataDetail.recordType)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">币种</div>
            <div class="input-wrap">{{dataDetail.sendCurrency || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">本次交易实际消耗的数量</div>
            <div class="input-wrap">{{dataDetail.gasUsed || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">本次交易实际消耗的单价</div>
            <div class="input-wrap">{{dataDetail.gasPrice || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">手续费</div>
            <div class="input-wrap">{{dataDetail.freePrice || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">用户输入的金额</div>
            <div class="input-wrap"><input v-model="dataDetail.enterPrice"></div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">实际获得金额</div>
            <div class="input-wrap">{{dataDetail.actualPrice || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">单价</div>
            <div class="input-wrap">{{dataDetail.unitPrice || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">数量</div>
            <div class="input-wrap">{{dataDetail.amount || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">订单号</div>
            <div class="input-wrap">{{dataDetail.orderNo || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">订单状态</div>
            <div class="input-wrap dropdown-wrap">
              <el-dropdown @command="handleCommand2">
              <span class="el-dropdown-link">
                {{ statusText}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
                <el-dropdown-menu class="menu" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in statusArr" :key="index">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">发送者返回的hash值</div>
            <div class="input-wrap"><input v-model="dataDetail.orderHash"></div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">汇总状态</div>
            <div class="input-wrap">{{$summaryStatus(dataDetail.summaryStatus)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">交易时间</div>
            <div class="input-wrap">{{$changeDate(dataDetail.transactionTime)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">失败/异常原因</div>
            <div class="input-wrap">{{dataDetail.failureCause || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">检查次数</div>
            <div class="input-wrap">{{dataDetail.checkCount || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">下次检查时间</div>
            <div class="input-wrap">{{$changeDate(dataDetail.nextCheckTime)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">创建时间</div>
            <div class="input-wrap">{{$changeDate(dataDetail.createDate)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">更新时间</div>
            <div class="input-wrap">{{$changeDate(dataDetail.updateDate)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">是否累计业绩</div>
            <div class="input-wrap">{{$isTotal(dataDetail.isTotal)}}</div>
          </div>
        </li>      <li class="li-item search">
        <div class="search-box">
          <div class="label">每枚ETH到USD的价格</div>
          <div class="input-wrap">{{dataDetail.ethExchangeRate || '-'}}</div>
        </div>
      </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">每笔交易系统收取的手续费</div>
            <div class="input-wrap">{{dataDetail.systemExchangeRate || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">转换后的金额</div>
            <div class="input-wrap">{{dataDetail.convertPrice || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">转换后的金额（税后）</div>
            <div class="input-wrap">{{dataDetail.convertPriceTax || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">系统钱包实际获得金额（税后）</div>
            <div class="input-wrap">{{dataDetail.actualPriceTax || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">购买SIE类型</div>
            <div class="input-wrap">{{$tradeType(dataDetail.tradeType)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">备注字段</div>
            <div class="input-wrap">{{dataDetail.remark || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">是否SDK下的订单</div>
            <div class="input-wrap">{{dataDetail.issdkorder === 1 ? '是' : '否'}}</div>
          </div>
        </li>
      </ul>
    </div>
    <el-dialog width="30%" :visible.sync="contactInformationDialog" :before-close="handleClose" title="重新转账">
      <div class="problem-box">
        <img src="../../../assets/img/warning.png"><label>确定重新转账？</label>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="showDialog = false">取 消</el-button>
        <el-button type="primary" @click="transferBtn()">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog width="30%" :visible.sync="showDialog">
      <div class="problem-box">
        <img src="../../../assets/img/warning.png"><label>确定保存？</label>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="showDialog = false">取 消</el-button>
        <el-button type="primary" @click="paySure()">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  import Dialog from '@/components/dialog/dialog'
  import { Message } from 'element-ui'
  export default {
    name: "transferLogDetail",
    data() {
      return{
        dataDetail: {},
        openfireArr: [
          {id: 0, text: '未注册'},
          {id: 1, text: '已注册'}
        ],//是否注册openfire用户
        synStatusArr: [
          {id: 0, text: '未注册'},
          {id: 1, text: '已注册'}
        ],//注册数据同步状态
        synStatusText: '',
        openfireText: '',
        openfire: '',//是否注册openfire用户
        genderText:  '',
        genderArr: [],//性别
        gender: '',
        statusArr: [],//账户状态
        statusText: '',
        tradeArr: [],//支付密码
        tradeText: '',
        mobileArr: [],//手机验证码
        mobileText: '',
        googleArr: [],//Gogole验证码
        googleText: '',
        userTypeArr: [],//用户类型
        userTypeText: '',
        loginPassword: '',
        contactInformationDialog: false,//修改用户上级弹窗
        showDialog: false,// 注销用户弹窗
        id: this.$route.query.id,
        refUserName: this.$route.query.refUserName,//上级用户名
        status: '',
        openTradePassword: '',//开启支付密码
        openMobileCode: '',//开启手机验证码
        openGoogleCode: '',//开启Google验证
        userType: '',//用户类型
        synStatus: '',//注册数据同步状态
      }
    },
    created() {
      this.getType()
      this.getData()
    },
    watch: {
      // Keyword: {
      //   handler(newName, oldName) {
      //     if (newName !== oldName) {
      //       this.bountyProductList = []
      //       this.isChange = true
      //     }
      //   },
      //   immediate: true
      // }
    },
    methods: {
      // 重新转账
      modiftyBtn(){
        this.contactInformationDialog = true
      },
      // 确定修改
      modifSure() {
        if(!this.id) {
          Message.error('请填写用户Id')
          return
        }
        if(!this.refUserName) {
          Message.error('请填写上级用户名')
          return
        }
        this.$fetch.post('/user/userAction/updateUserLevel', {
          userId: this.id,
          refUserName: this.refUserName
        }).then(res => {
          if(res.code === 0) {
            this.contactInformationDialog = false
            Dialog({
              title: '',
              content: res.msg,
              okFn: () => {
                this.getData()
              }
            })
          }
        }).catch(err => {
          console.log(err)
        })
      },
      // 注销
      cancelBtn() {
        this.showDialog = true
      },
      // 修改用户上级
      handleClose() {
        this.contactInformationDialog = false
      },
      //重新转账
      transferBtn(){
        this.$fetch.post(`/trade/transferRecord/updateTransferRecord`,{
          id: this.id,
          checkCount: this.dataDetail.checkCount,
        }).then(res => {
          if(res.code === 0) {
            this.contactInformationDialog = false
            Dialog({
              title: '',
              content: res.msg,
            })
          }
        }).catch(err => {
          console.log(err)
        })
      },
      // 保存
      paySure() {
        this.$fetch.post(`/trade/transferRecord/updateTransferRecord`,{
          id: this.id,
          orderHash: this.dataDetail.orderHash,
          orderStatus:  this.dataDetail.orderStatus,
          enterPrice: this.dataDetail.enterPrice,
          checkCount: this.dataDetail.checkCount,
        }).then(res => {
          if(res.code === 0) {
            this.showDialog = false
            Dialog({
              title: '',
              content: res.msg,
            })
          }
        }).catch(err => {
          console.log(err)
        })
      },
      // 枚举
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=trade.TransferRecord$RecordTypeEnum;trade.TransferRecord$CurrencyTypeEnum;trade.TransferRecord$IsTotalEnum;trade.TransferRecord$OrderStatusEnum;trade.TransferRecord$SummaryStatusEnum;trade.TransferRecord$TradeTypeEnum&flag=false&state=1`).then(res => {
          if (res.code === 0) {
            this.statusArr = res.data.data.OrderStatusEnum
          }
        })
      },
      // 注册数据同步状态
      handleCommand8(command) {
        this.synStatusText = command.text
        this.synStatus = command.id
      },
      // 是否注册openfire用户
      handleCommand7(command) {
        this.openfireText = command.text
        this.openfire = command.id
      },
      // 用户类型
      handleCommand6(command) {
        this.userTypeText = command.text
        this.userType = command.id
      },
      // Gogole验证码
      handleCommand5(command) {
        this.googleText = command.text
        this.openGoogleCode = command.id
      },
      // 手机验证码
      handleCommand4(command) {
        this.mobileText = command.text
        this.openMobileCode = command.id
      },
      // 支付密码
      handleCommand3(command) {
        this.tradeText = command.text
        this.openTradePassword = command.id
      },
      // 性别
      handleCommand(command) {
        this.genderText = command.text
        this.gender = command.id
      },
      //账户状态
      handleCommand2(command) {
        this.statusText = command.text
        this.dataDetail.orderStatus = command.id
      },
      verify() {
        // if(!this.sendUserName) {
        //   Message.error('请填写发送者用户名')
        //   return
        // }
        // if(!this.acceptUserName) {
        //   Message.error('请填写接收者用户名')
        //   return
        // }
        // if(!this.amount) {
        //   Message.error('请填写金额')
        //   return
        // }
        // if(!this.currencyText) {
        //   Message.error('请选择币种')
        //   return
        // }
        // if(!this.code) {
        //   Message.error('请填写安全码')
        //   return
        // }
        return true
      },
      // 保存
      saveBtn(){
        this.showDialog = true
      },
      getData(){
        this.$fetch.post(`/trade/transferRecord/preSelectTransferRecord`,{
          id: this.id
        }).then(res => {
          this.dataDetail = res.data.resultData
          this.tradeStatus( this.dataDetail.orderStatus)
        })
      },
      tradeStatus(val) {
        if(Number(val) === 0)  return this.statusText = '待回调'
        if (Number(val) === 1) return  this.statusText = '创建'
        if (Number(val) === 2) return  this.statusText = '用户已确认转账'
        if (Number(val) === 3) return  this.statusText = '系统检查中'
        if (Number(val) === 4) return  this.statusText = '系统检查失败'
        if (Number(val) === 5) return  this.statusText = '系统检查异常'
        if (Number(val) === 6) return  this.statusText = '系统检查成功'
        if (Number(val) === 7) return  this.statusText = '转账成功'
        if (Number(val) === 8) return  this.statusText = '转账失败'
        if (Number(val) === 9) return  this.statusText = '转账取消'
        if (Number(val) === 10) return  this.statusText = '创建(转出)'
        if (Number(val) === 11) return  this.statusText = '以太坊处理中(请求以太坊)'
        if (Number(val) === 12) return  this.statusText = '以太坊检查中'
        if (Number(val) === 13) return  this.statusText = '转出成功'
        if (Number(val) === 14) return  this.statusText = '系统扣款失败'
        if (Number(val) === 15) return  this.statusText = '以太坊转出失败'
        if (Number(val) === 16) return  this.statusText = '交易成功'
        if (Number(val) === 17) return  this.statusText = '系统取消超时订单'
        if (Number(val) === 18) return  this.statusText = '转出系统审核中'
        if (Number(val) === 19) return  this.statusText = '转出系统审核失败'
        if (Number(val) === 20) return  this.statusText = '转出中间状态'
        if (Number(val) === 21) return  this.statusText = 'BTC转出创建'
        if (Number(val) === 22) return  this.statusText = 'BTC转出异常'
        if (Number(val) === 23) return  this.statusText = 'BTC转出成功'
        if (Number(val) === 24) return  this.statusText = '调用api转账接口失败'
        return  this.statusText = '未选择'
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
  .zoom-enter-to{
    transition: none;
  }
  .el-dropdown-menu{
    width 260px
  }
  .dialog-input{
    width 240px!important
  }
  .problem-box{
    display flex
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
    padding 0 20px!important
    .search{
      padding-bottom 0
      .search-box{
        width 100%
        margin-top 0
        margin-bottom 20px
        .el-input{
          display flex
          align-items center
          width 80%!important
          .el-input__inner{
            margin-right 0!important
            width 100%!important
          }
          .el-input__icon{
            line-height 35px!important
          }
        }
        .curreny-wrap{
          cursor pointer
          text-align center
          line-height 40px
          background-color #fff
          border: 1px solid #DCDFE6;
          border-radius: 6px;
          padding: 0 10px;
          font-size 15px
          height: 40px;
        }
        .input-wrap{
          width 80%
          &.input-main{
            width 70%
          }
          input{
            margin-right 0!important
            width 100%
          }
        }
      }
    }
  }
  .wrap
    background-color: @white;
  .search
    padding-bottom 0
    position relative
    display flex
    flex-direction row
    flex-wrap wrap
    .search-box
      margin-bottom 0
      display flex
      flex-direction row
      align-items center
      .input-wrap
        width 280px
        input
          width 100%
        textarea
          width 260px
          height 100px
          padding 10px
          border 1px solid #DCDFE6
          background #fff
      .label
        width 200px
        color #b2b3bc
      .dropdown-wrap
        width 280px!important
        cursor pointer
        text-align center
        line-height 40px
        margin-right: 20px;
        background-color #fff
        border: 1px solid #DCDFE6;
        border-radius: 6px;
        padding: 0 10px;
        font-size 15px
        height: 40px;
    .btns
      margin-top 30px
      margin-right 50px
      .btn
        cursor pointer
</style>

<style scoped>
  >>>.freezeListWrap .empty {
    top: 20px;
  }
  
  /*>>>.el-date-editor.el-input, .el-date-editor.el-input__inner {*/
  /*padding: 0 10px !important;*/
  /*margin-right: 20px !important;*/
  /*width: 130px !important;*/
  /*height: 25px;*/
  /*}*/
  
  >>> .el-dialog {
    margin: 0 auto;
    width: 80%;
    box-shadow: none;
  }
  >>>.dropdown-wrap{
    min-width: 160px !important;
  }
  /*>>> .el-dialog .el-dialog__headerbtn .el-icon-close:before {*/
  /*  border: 2px solid #ccc;*/
  /*  border-radius: 100%;*/
  /*}*/
  
  /*>>> .el-date-editor .el-input__inner {*/
  /*padding: 0 10px !important;*/
  /*}*/
  
  textarea {
    font-size: 14px;
    padding: 16px 0 0 18px;
    color: #000;
  }
  
  textarea::-webkit-input-placeholder {
    color: #b3b3b3;
  }
  
  .input:-moz-placeholder {
    color: #b3b3b3;
  }
  
  .input:-ms-input-placeholder {
    color: #b3b3b3;
  }
</style>>
