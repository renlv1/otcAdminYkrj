<template>
    <div class="c-page mar-right transaction-list">
        <div class="crumb">
            <span><i style="color: #b8b9c1;">转账记录 /</i> 查看总账</span>
            <div class="add-btn-w">
            </div>
        </div>
        <div class="page-wrap">
            <div class="tite"><i class="icon"></i>APP账户统计</div>
            <div class="web-table">
                    <table class="center">
                        <tr class="thead">
                            <td>币种</td>
                            <td>收入总金额</td>
                            <td>支出总金额</td>
                            <td>导入APP数据余额</td>
                        </tr>
                        <tr class="tbody-tr">
                            <td>USD</td>
                            <td>{{ledger.appGetBillUSDIn || '0'}}</td>
                            <td>{{ledger.appGetBillUSDOut || '0'}}</td>
                            <td>{{ledger.userAccountCopyUSD || '0'}}</td>
                        </tr>
                        <tr class="tbody-tr">
                            <td>ETH</td>
                            <td>{{ledger.appGetBillETHIn || '0'}}</td>
                            <td>{{ledger.appGetBillETHOut || '0'}}</td>
                            <td>{{ledger.userAccountCopyETH || '0'}}</td>
                        </tr>
                        <tr class="tbody-tr">
                            <td>SIE</td>
                            <td>{{ledger.appGetBillSIEIn || '0'}}</td>
                            <td>{{ledger.appGetBillSIEOut || '0'}}</td>
                            <td>{{ledger.userAccountCopySIE || '0'}}</td>
                        </tr>
                        <tr class="tbody-tr">
                            <td>BTC</td>
                            <td>{{ledger.appGetBillBTCIn || '0'}}</td>
                            <td>{{ledger.appGetBillBTCOut || '0'}}</td>
                            <td>{{ledger.userAccountCopyBTC || '0'}}</td>
                        </tr>
                        <!--<div class="ajax-loading" v-show="loadShow">
                            <div class="loading-content">
                                <img src="@img/loading2.gif" alt="">
                                <p>正在加载中...</p>
                            </div>
                        </div>-->
                    </table>
                </div>
            <div class="tite"><i class="icon"></i>APP币种统计</div>
            <div class="currency-statistics">
                <div class="currency-type">USD</div>
                <div class="income">
                    <div class="tit">收入</div>
                    <div class="content-box">
                        <div class="information">
                            <div class="li">
                                <span class="headings">内部转入</span>
                                <span class="parameter">{{ledger.usdBuyTo || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">交易所卖SIE</span>
                                <span class="parameter">{{ledger.sellSIERecord || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">充值</span>
                                <span class="parameter">{{ledger.usdAmount || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">传统交易所卖SIE</span>
                                <span class="parameter">{{ledger.newSellSIERecord || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">传统交易所卖BTC</span>
                                <span class="parameter">{{ledger.newSellBTCRecord || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">提现退款收款</span>
                                <span class="parameter">{{ledger.drawFailReturn || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">提现申诉退款收款</span>
                                <span class="parameter">{{ledger.drawProblemFailReturn || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">第三方转入</span>
                                <span class="parameter">{{ledger.threeSum || '0'}}</span>
                            </div>
                        </div>
                        <div class="information">
                            <div class="li">
                                <span class="headings">外部转入</span>
                                <span class="parameter">{{ledger.usdTransferTo || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">共享者收入(提现)</span>
                                <span class="parameter">{{ledger.insideShare || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">交易奖励</span>
                                <span class="parameter">{{ledger.tradeReward || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">传统交易所卖ETH</span>
                                <span class="parameter">{{ledger.newSellETHRecord || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">系统后台收入USD</span>
                                <span class="parameter">{{ledger.transferBackGetUSD || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">充值申诉退款收款</span>
                                <span class="parameter">{{ledger.depositProblemFailReturn || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">提现审核失败退款收款</span>
                                <span class="parameter">{{ledger.drawAuditFailReturn || '0'}}</span>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="income">
                    <div class="tit">支出</div>
                    <div class="content-box">
                        <div class="information">
                            <div class="li">
                                <span class="headings">内部转出</span>
                                <span class="parameter">{{ledger.usdInSideOut || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">交易所买SIE</span>
                                <span class="parameter">{{ledger.buySIERecord || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">传统交易所买ETH</span>
                                <span class="parameter">{{ledger.newBuyETHRecord || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">系统后台支出USD</span>
                                <span class="parameter">{{ledger.transferBackLoseUSD || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">传统交易所买BTC</span>
                                <span class="parameter">{{ledger.newSellBTCRecord || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">购买SIE</span>
                                <span class="parameter">{{ledger.usdToSie || '0'}}</span>
                            </div>
                        </div>
                        <div class="information">
                            <div class="li">
                                <span class="headings">提现</span>
                                <span class="parameter">{{ledger.resultDraw || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">传统交易所买SIE</span>
                                <span class="parameter">{{ledger.newBuySIERecord || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">传统交易所买BTC</span>
                                <span class="parameter">{{ledger.newBuyBTCRecord || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">共享者支出(充值)</span>
                                <span class="parameter">{{ledger.outsideShare || '0'}}</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="currency-statistics">
                <div class="currency-type">SIE</div>
                <div class="income">
                    <div class="tit">收入</div>
                    <div class="content-box">
                        <div class="information">
                            <div class="li">
                                <span class="headings">内部转入</span>
                                <span class="parameter">{{ledger.sieBuyTo || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">克莱因瓶交易购入sie返还</span>
                                <span class="parameter">{{ledger.tradeReturnSieBuy || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">吃饼游戏奖励</span>
                                <span class="parameter">{{ledger.eatGameReward || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">优惠券兑换</span>
                                <span class="parameter">{{ledger.DiscountsToSie || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">红包收款</span>
                                <span class="parameter">{{ledger.redEnvInside || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">群应用收款</span>
                                <span class="parameter">{{ledger.groupReceive || '0'}}</span>
                            </div>
                        </div>
                        <div class="information">
                            <div class="li">
                                <span class="headings">以太坊转入</span>
                                <span class="parameter">{{ledger.sieInSideTransferTo || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">PK盈利</span>
                                <span class="parameter">{{ledger.pkProfit || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">购买SIE</span>
                                <span class="parameter">{{ledger.sieAmount || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">传统交易所购买</span>
                                <span class="parameter">{{ledger.newBuyUSDRecord || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">系统后台收入SIE</span>
                                <span class="parameter">{{ledger.transferBackGetSIE || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">充值</span>
                                <span class="parameter">{{ledger.sieInside || '0'}}</span>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="income">
                    <div class="tit">支出</div>
                    <div class="content-box">
                        <div class="information">
                            <div class="li">
                                <span class="headings">转出内部转账</span>
                                <span class="parameter">{{ledger.outInnerTransfer || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">交易所卖出</span>
                                <span class="parameter">{{ledger.buyUSDRecord || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">开通AI</span>
                                <span class="parameter">{{ledger.openAIPrice || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">提现</span>
                                <span class="parameter">{{ledger.sieOutside || '0'}}</span>
                            </div>
                        </div>
                        <div class="information">
                            <div class="li">
                                <span class="headings">以太坊转出</span>
                                <span class="parameter">{{ledger.sideTransferTo || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">系统后台支出SIE</span>
                                <span class="parameter">{{ledger.transferBackLoseSIE || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">传统交易所卖出</span>
                                <span class="parameter">{{ledger.newSellUSDRecord || '0'}}</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="currency-statistics">
                <div class="currency-type">ETH</div>
                <div class="income">
                    <div class="tit">收入</div>
                    <div class="content-box">
                        <div class="information">
                            <div class="li">
                                <span class="headings">内部转入</span>
                                <span class="parameter">{{ledger.ethBuyTo || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">交易所购买</span>
                                <span class="parameter">{{ledger.buyUSDbySellRecord || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">系统后台收入ETH</span>
                                <span class="parameter">{{ledger.transferBackGetETH || '0'}}</span>
                            </div>
                        </div>
                        <div class="information">
                            <div class="li">
                                <span class="headings">以太坊转入</span>
                                <span class="parameter">{{ledger.ethTransferTo || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">传统交易所购买</span>
                                <span class="parameter">{{ledger.newBuyUSDbySellRecord || '0'}}</span>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="income">
                    <div class="tit">支出</div>
                    <div class="content-box">
                        <div class="information">
                            <div class="li">
                                <span class="headings">内部转出</span>
                                <span class="parameter">{{ledger.ethInSideOut || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">交易所卖出</span>
                                <span class="parameter">{{ledger.sellUSDbyBuyRecord || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">系统后台支出ETH</span>
                                <span class="parameter">{{ledger.transferBackLoseETH || '0'}}</span>
                            </div>
                        </div>
                        <div class="information">
                            <div class="li">
                                <span class="headings">以太坊转出</span>
                                <span class="parameter">{{ledger.ethsideTransferTo || '0'}}</span>
                            </div>
                            <div class="li">
                                <span class="headings">传统交易所卖出</span>
                                <span class="parameter">{{ledger.newSellUSDbyBuyRecord || '0'}}</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="currency-statistics">
                <div class="currency-type">BTC</div>
                <div class="income">
                    <div class="tit">收入</div>
                    <div class="content-box">
                        <div class="information">
                            <div class="li">
                                <span class="headings">传统交易所购买</span>
                                <span class="parameter">{{ledger.newBuyBTCbySellRecord || '0'}}</span>
                            </div>
                        </div>
                        <div class="information">
                            <div class="li">
                                <span class="headings">系统后台收入BTC</span>
                                <span class="parameter">{{ledger.transferBackGetBTC || '0'}}</span>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="income">
                    <div class="tit">支出</div>
                    <div class="content-box">
                        <div class="information">
                            <div class="li">
                                <span class="headings">传统交易所卖出</span>
                                <span class="parameter">{{ledger.newSellBTCbyBuyRecord || '0'}}</span>
                            </div>
                        </div>
                        <div class="information">
                            <div class="li">
                                <span class="headings">系统后台支出BTC</span>
                                <span class="parameter">{{ledger.transferBackLoseBTC || '0'}}</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="currency-statistics">
                <div class="income">
                    <div class="content-box" style="border-left: none;">
                        <div class="information">
                            <div class="li">
                                <span class="headings">达尔文购买数量</span>
                                <span class="parameter">{{ledger.drwNumber || '0'}}</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
  import { Message } from 'element-ui'
  import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        ledger: this.$store.state.ledgerObj,
        loadShow: true
      }
    },
    created() {
      //this.confirmLedger()
    },
    methods: {
      //跳查看总账
      confirmLedger () {
        //this.$store.commit('SET_LOADING', true)
        if (this.accountName === '') {
          Message ('用户名不能为空!')
        } else {
          this.$fetch.post(`/trade/transferRecord/preCheckAuditInfo?userName=${this.accountName}`).then(res => {
            if (res.code === 0) {
              let ledger = res.data
              if (ledger) {
                this.ledger = ledger
                this.loadShow = false
              }
            } else {
              Message(res.msg)
            }
          })
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
    .crumb
        display flex
        justify-content space-between
        align-items center
        margin-bottom: 20px;
        margin-top: -13px;
        .add-btn-w
            display: flex
            align-items center
            .add-btn
                width: 100px
                margin-left: 20px
    .page-wrap
      >>> .el-pagination-wrap
             display none !important
      .tite
         font-size 16px
         font-weight 500
         color #121212
         padding 20px 0
         i 
           display inline-block
           width: 6px
           height: 6px
           border-radius 50%
           background-color #121212
           vertical-align middle
           margin-right 10px
      .web-table
            padding 0
            .web-loading1 
                  width 160px
                  height 30px
                  display flex
                  align-items center
                  img
                    display inline-block
                    width 30px
                  span
                    flex 1
                    text-align left
                    padding-left 10px
          .ajax-loading
              position fixed
              z-index 999999
              left 0
              top 0
              width 100%
              height 100%
              .loading-content
                  position: absolute
                  width 150px
                  height 150px
                  background rgba(0,0,0,.8)
                  text-align center;
                  left 50%
                  top 50%
                  transform: translate(-50%, -50%)
                  .vCenter
                    flex-direction column
                    justify-content center
                    color #fff
                    border-radius 10px
              img
                width: 80px;
                text-align: left;
                margin-bottom: 16px;
      .currency-statistics
         border 1px solid #DCDFE6
         margin-bottom 30px
         .currency-type
               height 50px
               line-height 50px
               background-color #edeff2
               border-bottom 1px solid #DCDFE6
               padding-left 20px
         .income
            display flex
            height 100%
            align-items center
            justify-content flex-start
            background-color #edeff2
            border-bottom 1px solid #DCDFE6
            &:last-child
                border none
            .tit
               width 60px
               height 100%
               text-align center
            .content-box
                flex 1
                background-color #fff
                border-left 1px solid #DCDFE6
                padding 20px 
                display flex
                align-items flex-start
                justify-content flex-start
                .information
                   line-height 30px
                   &:nth-child(1)
                       width 50%
                   .li
                      .headings
                         display inline-block
                         width 164px
                         color #b8b9c1
</style>
