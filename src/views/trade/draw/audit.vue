<template>
  <div class="c-page mar-right transaction-list">
    <div style="z-index: 9999" class="web-loading1 web-load2" v-show="loadShow">
      <img src="~@img/loading.gif" />
    </div>
    <div class="crumb detail-page">
      <div class="title-left">
        <router-link to="/trade/draw" class="title-m">提现订单 </router-link>
        <span > / 审核页面</span>
      </div>
      <div class="btn-right">
        <span class="pay-money" @click="failedBtn()">审核失败</span>
        <div class="pay-money success-audit" @click="successBtn()">审核成功</div>
      </div>
    </div>
    <div class="page-wrap detail-cont">
      <ul class="ul-w ">
         <div class="comm-title">APP账户统计</div>
         <div class="web-table">
          <table>
            <tr class="thead">
              <td >币种</td>
              <td>收入总金额</td>
              <td >支出总金额</td>
              <td>导入APP数据余额</td>
            </tr>
            <tr>
              <td>USD</td>
              <td>{{detailData.appGetBillUSDIn}}</td>
              <td>{{detailData.appGetBillUSDOut}}</td>
              <td>{{detailData.userAccountCopyUSD}}</td>
            </tr>
            <tr>
              <td>ETH</td>
              <td>{{detailData.appGetBillETHIn}} </td>
              <td>{{detailData.appGetBillETHOut}}</td>
              <td>{{detailData.userAccountCopyETH || 0}} </td>
            </tr>
            <tr>
              <td>SIE</td>
              <td>{{detailData.appGetBillSIEIn}}</td>
              <td>{{detailData.appGetBillSIEOut}}</td>
              <td>{{detailData.userAccountCopySIE}}</td>
            </tr>
            <tr>
              <td>BTC</td>
              <td>{{detailData.appGetBillBTCIn}}</td>
              <td>{{detailData.appGetBillBTCOut}}</td>
              <td>{{detailData.userAccountCopyBTC || 0}}</td>
            </tr>
          </table>
      </div>
         <div class="comm-title">APP币种统计</div>
         <ul class="ul-currency">
          <li class="li-first">USD</li>
          <li class="li-second li-hei">
            <div class="income">收入</div>
            <div class="right-cont">
              <ul class="ul-cont ul-first">
                <li class="li-cont">
                  <div class="li-tit">内部转入</div>
                  <div class="li-da">{{detailData.usdBuyTo || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">交易所卖SIE</div>
                  <div class="li-da">{{detailData.sellSIERecord}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">充值</div>
                  <div class="li-da">{{detailData.usdAmount}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">传统交易所卖SIE</div>
                  <div class="li-da">{{detailData.newSellSIERecord}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">传统交易所卖BTC</div>
                  <div class="li-da">{{detailData.newSellBTCRecord}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">提现退款收款</div>
                  <div class="li-da">{{detailData.drawFailReturn}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">提现申诉退款收款</div>
                  <div class="li-da">{{detailData.drawProblemFailReturn || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">第三方转入</div>
                  <div class="li-da">{{detailData.threeSum}}</div>
                </li>
              </ul>
              <ul class="ul-cont">
                <li class="li-cont">
                  <div class="li-tit">外部转入</div>
                  <div class="li-da">{{detailData.usdTransferTo || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">共享者收入（提现）</div>
                  <div class="li-da">{{detailData.insideShare}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">交易奖励</div>
                  <div class="li-da">{{detailData.tradeReward}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">传统交易所卖ETH</div>
                  <div class="li-da">{{detailData.newSellETHRecord}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">系统后台收入USD</div>
                  <div class="li-da">{{detailData.transferBackGetUSD || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">充值申诉退款收款</div>
                  <div class="li-da">{{detailData.depositProblemFailReturn || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">提现审核失败退款收款</div>
                  <div class="li-da">{{detailData.drawAuditFailReturn || 0}}</div>
                </li>
              </ul>
            </div>
          </li>
          <li class="li-second">
            <div class="income">支出</div>
            <div class="right-cont">
              <ul class="ul-cont ul-first">
                <li class="li-cont">
                  <div class="li-tit">内部转出</div>
                  <div class="li-da">{{detailData.usdInSideOut}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">交易所买SIE</div>
                  <div class="li-da">{{detailData.buySIERecord}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">传统交易所买ETH</div>
                  <div class="li-da">{{detailData.newBuyETHRecord || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">系统后台支出USD</div>
                  <div class="li-da">{{detailData.transferBackLoseUSD || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">购买SIE</div>
                  <div class="li-da">{{detailData.usdToSie || 0}}</div>
                </li>
              </ul>
              <ul class="ul-cont">
                <li class="li-cont">
                  <div class="li-tit">提现</div>
                  <div class="li-da">{{detailData.resultDraw}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">传统交易所买SIE</div>
                  <div class="li-da">{{detailData.newBuySIERecord || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">传统交易所买BTC</div>
                  <div class="li-da">{{detailData.newBuyBTCRecord || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">共享者支出（充值）</div>
                  <div class="li-da">{{detailData.outsideShare || 0}}</div>
                </li>
              </ul>
            </div>
          </li>
          </ul>
         <ul class="ul-currency ul-box">
          <li class="li-first">SIE</li>
          <li class="li-second li-hei">
            <div class="income">收入</div>
            <div class="right-cont">
              <ul class="ul-cont ul-first">
                <li class="li-cont">
                  <div class="li-tit">内部转入</div>
                  <div class="li-da">{{detailData.sieBuyTo || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">克莱因瓶交易购入sie返还</div>
                  <div class="li-da">{{detailData.tradeReturnSieBuy}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">吃饼游戏奖励</div>
                  <div class="li-da">{{detailData.eatGameReward}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">优惠券兑换</div>
                  <div class="li-da">{{detailData.DiscountsToSie || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">红包收款</div>
                  <div class="li-da">{{detailData.redEnvInside || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">群应用收款</div>
                  <div class="li-da">{{detailData.groupReceive || 0}}</div>
                </li>
              </ul>
              <ul class="ul-cont">
                <li class="li-cont">
                  <div class="li-tit">以太坊转入</div>
                  <div class="li-da">{{detailData.sieInSideTransferTo || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">pk盈利</div>
                  <div class="li-da">{{detailData.pkProfit || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">购买SIE</div>
                  <div class="li-da">{{detailData.sieAmount || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">传统交易所购买</div>
                  <div class="li-da">{{detailData.newBuySIE || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">系统后台收入SIE</div>
                  <div class="li-da">{{detailData.transferBackGetSIE || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">充值</div>
                  <div class="li-da">{{detailData.sieInside || 0}}</div>
                </li>
              </ul>
            </div>
          </li>
          <li class="li-second">
            <div class="income">支出</div>
            <div class="right-cont">
              <ul class="ul-cont ul-first">
                <li class="li-cont">
                  <div class="li-tit">转出内部转账</div>
                  <div class="li-da">{{detailData.outInnerTransfer || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">交易所卖出</div>
                  <div class="li-da">{{detailData.buyUSDRecord || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">开通AI</div>
                  <div class="li-da">{{detailData.openAIPrice || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">提现</div>
                  <div class="li-da">{{detailData.sieOutside || 0}}</div>
                </li>
              </ul>
              <ul class="ul-cont">
                <li class="li-cont">
                  <div class="li-tit">以太坊转出</div>
                  <div class="li-da">{{detailData.sideTransferTo || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">系统后台支出SIE</div>
                  <div class="li-da">{{detailData.transferBackLoseSIE || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">传统交易所卖出</div>
                  <div class="li-da">{{detailData.newSellSIE || 0}}</div>
                </li>
              </ul>
            </div>
          </li>
        </ul>
         <ul class="ul-currency ul-box">
          <li class="li-first">ETH</li>
          <li class="li-second li-hei ul-samll">
            <div class="income  in-samll">收入</div>
            <div class="right-cont">
              <ul class="ul-cont ul-first">
                <li class="li-cont">
                  <div class="li-tit">内部转入</div>
                  <div class="li-da">{{detailData.ethBuyTo || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">交易所购买</div>
                  <div class="li-da">{{detailData.buyUSDbySellRecord || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">系统后台收入ETH</div>
                  <div class="li-da">{{detailData.transferBackGetETH || 0}}</div>
                </li>
              </ul>
              <ul class="ul-cont">
                <li class="li-cont">
                  <div class="li-tit">以太坊转入</div>
                  <div class="li-da">{{detailData.ethTransferTo || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">传统交易所购买</div>
                  <div class="li-da">{{detailData.buyUSDbySellRecord || 0}}</div>
                </li>
              </ul>
            </div>
          </li>
          <li class="li-second ul-samll">
            <div class="income in-samll">支出</div>
            <div class="right-cont">
              <ul class="ul-cont ul-first">
                <li class="li-cont">
                  <div class="li-tit">内部转出</div>
                  <div class="li-da">{{detailData.ethInSideOut || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">交易所卖出</div>
                  <div class="li-da">{{detailData.sellUSDbyBuyRecord || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">系统后台支出ETH</div>
                  <div class="li-da">{{detailData.transferBackLoseETH || 0}}</div>
                </li>
              </ul>
              <ul class="ul-cont">
                <li class="li-cont">
                  <div class="li-tit">以太坊转出</div>
                  <div class="li-da">{{detailData.ethsideTransferTo || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">传统交易所卖出</div>
                  <div class="li-da">{{detailData.sellUSDbyBuyRecord || 0}}</div>
                </li>
              </ul>
            </div>
          </li>
        </ul>
         <ul class="ul-currency ul-box">
          <li class="li-first">BTC</li>
          <li class="li-second li-hei ul-samll">
            <div class="income  in-samll">收入</div>
            <div class="right-cont">
              <ul class="ul-cont ul-first">
                <li class="li-cont">
                  <div class="li-tit">传统交易所购买</div>
                  <div class="li-da">{{detailData.newBuyBTCbySellRecord || 0}}</div>
                </li>
              </ul>
              <ul class="ul-cont">
                <li class="li-cont">
                  <div class="li-tit">系统后台收入BTC</div>
                  <div class="li-da">{{detailData.transferBackGetBTC || 0}}</div>
                </li>
              </ul>
            </div>
          </li>
          <li class="li-second ul-samll">
            <div class="income in-samll">支出</div>
            <div class="right-cont">
              <ul class="ul-cont ul-first">
                <li class="li-cont">
                  <div class="li-tit">传统交易所卖出</div>
                  <div class="li-da">{{detailData.newSellBTCbyBuyRecord || 0}}</div>
                </li>
                <li class="li-cont">
                  <div class="li-tit">系统后台支出BTC</div>
                  <div class="li-da">{{detailData.transferBackLoseBTC || 0}}</div>
                </li>
              </ul>
              <ul class="ul-cont">
                <li class="li-cont">
                  <div class="li-tit">达尔文购买数量</div>
                  <div class="li-da">{{detailData.drwNumber || 0}}</div>
                </li>
              </ul>
            </div>
          </li>
        </ul>
      </ul>
    </div>
    <el-dialog width="30%" :visible.sync="auditDialog">
      <div class="problem-box">
        <img src="../../../assets/img/warning.png"><label>确认审核成功？</label>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="auditDialog = false">取 消</el-button>
        <el-button type="primary" @click="paySure(1)">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog width="30%" :visible.sync="failDialog" title="审核失败">
      <div class="search textarea-main">
        <div class="search-box">
          <div class="label">账户用户名</div>
          <div class="input-wrap input-main">
            <el-input
            type="textarea"
            placeholder="请输入失败原因"
            v-model="textarea"
            maxlength="30"
            show-word-limit
          >
          </el-input>
          </div>
        </div>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="failDialog = false">取 消</el-button>
        <el-button type="primary" @click="paySure(2)">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  import Dialog from '@/components/dialog/dialog'
  export default {
    name: "helpDetail",
    data() {
      return{
        loadShow: true,
        textarea: '',
        failDialog: false,
        auditDialog: false,
        id: this.$route.query.id,
        detailData: {}
      }
    },
    created(){
      this.getDetail()
    },
    methods: {
      paySure(index) {
        let choose = ''
        if(index === 1){
          choose = 1
        } else if(index === 2) {
          if (!this.textarea) {
            this.$message.error('请输入失败原因!')
            return
          } else {
            choose = 2
          }
        }
        this.$fetch.post('/finance/draw/auditInfo',{
          id: this.id,
          auditDesc: this.textarea,
          choose:choose
        }).then(res => {
          this.auditDialog = false
          this.failDialog  = false
          Dialog({
            title: '',
            content: res.msg,
          })
        })
      },
      successBtn() {
        this.auditDialog = true
      },
      failedBtn() {
        this.failDialog  =true
      },
      getDetail() {
        this.$fetch.post('/finance/draw/preAuditInfo', {
          id: this.id
        }).then(res => {
          if(res.code === 0) {
            this.loadShow = false
            this.detailData = res.data
          }
        })
      }
    },
  }
</script>

<style lang="less" scoped>
  .el-dialog__body{
    padding: 20px !important;
  }
</style>
<style lang="stylus" rel="stylesheet/stylus" scoped>
  .editor-wrap{
    position relative
    width 80%
    height 450px
    .textNumber{
      position absolute
      right 12px
      bottom 40px
    }
    .upload{
      .el-upload{
        .el-icon-plus{
          opacity 0
          height 24px
          width 28px
          position absolute!important
          left 220px!important
          top 11px!important
        }
      }
    }
  }
  .quill-editor{
    height: calc(100% - 66px);
  }
  .zoom-enter-to{
    transition: none;
  }
  .label{
    white-space: nowrap;
    margin-right: 60px;
  }
  .search-box{
    display: flex;
    justify-content: flex-start;
    width: 100%;
  }
  .quill-editor{
    width: 100%;
  }
</style>
