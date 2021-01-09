<template>
    <div class="c-page mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>赏金订单记录 / <span class="tit">记录详情</span></span>
                <div class="preserve" v-if="objData.status === 2 && objData.callBackState === 4" @click="theCallback">重新回调</div>
                <div class="preserve" v-else-if="((objData.status === 1) && (objData.confirmState === 2 || objData.confirmState === 3))" @click="reconfirm()">重新确认支付状态</div>
                <div class="preserve" v-else-if="objData.status === 3" @click="backPay()">重新支付</div>
            </div>
            <div class="main-content">
                <div class="search-box">
                    <div class="label">ID</div>
                    <div class="input-wrap">{{objData.id}}</div>
                </div>
                <div class="search-box">
                    <div class="label">发送者用户名</div>
                    <div class="input-wrap">{{objData.sendUserName}}</div>
                </div>
                <div class="search-box">
                    <div class="label">发送者用户地址</div>
                    <div class="input-wrap">{{objData.sendUserAddress}}</div>
                </div>
                <div class="search-box">
                    <div class="label">接收者用户名</div>
                    <div class="input-wrap">{{objData.receiveUserName}}</div>
                </div>
                <div class="search-box">
                    <div class="label">接收者用户地址</div>
                    <div class="input-wrap">{{objData.receiveUserAddress}}</div>
                </div>
                <div class="search-box">
                    <div class="label">支付金额</div>
                    <div class="input-wrap">{{objData.payAmount}}</div>
                </div>
                <div class="search-box">
                    <div class="label">币种</div>
                    <div class="input-wrap">{{objData.currency}}</div>
                </div>
                <div class="search-box">
                    <div class="label">数据来源ID</div>
                    <div class="input-wrap">{{objData.sourceOrderId}}</div>
                </div>
                <div class="search-box">
                    <div class="label">支付类型</div>
                    <div class="input-wrap">{{paymentTypeFn(objData.paymentType)}}</div>
                </div>
                <div class="search-box">
                    <div class="label">支付方</div>
                    <div class="input-wrap"><span v-if="objData.payWhere === 1">本平台</span><span v-if="objData.payWhere === 2">秘密平台</span></div>
                </div>
                <div class="search-box">
                    <div class="label">状态</div>
                    <div class="input-wrap">{{statusFn(objData.status)}}</div>
                </div>
                <div class="search-box">
                    <div class="label">账单ID</div>
                    <div class="input-wrap">{{objData.payTranId}}</div>
                </div>
                <div class="search-box">
                    <div class="label">回调次数</div>
                    <div class="input-wrap">{{objData.callBackTime}}</div>
                </div>
                <div class="search-box">
                    <div class="label">回调状态</div>
                    <div class="input-wrap">{{callBackStateFn(objData.callBackState)}}</div>
                </div>
                <div class="search-box">
                    <div class="label">备注</div>
                    <div class="input-wrap">{{objData.remark}}</div>
                </div>
                <div class="search-box">
                    <div class="label">下次回调时间</div>
                    <div class="input-wrap">{{objData.nextCallBackDate}}</div>
                </div>
                <div class="search-box">
                    <div class="label">支付确认次数</div>
                    <div class="input-wrap">{{objData.confirmTime}}</div>
                </div>
                <div class="search-box">
                    <div class="label">支付确认状态</div>
                    <div class="input-wrap">{{confirmStateFn(objData.confirmState)}}</div>
                </div>
                <div class="search-box">
                    <div class="label">下次确认时间</div>
                    <div class="input-wrap">{{objData.nextConfirmDate}}</div>
                </div>
                <div class="search-box">
                    <div class="label">pic销毁标识</div>
                    <div class="input-wrap">{{picDestroyFlagFn(objData.picDestroyFlag)}}</div>
                </div>
                <div class="search-box">
                    <div class="label">销毁pic的hash</div>
                    <div class="input-wrap">{{objData.picDestoryHash}}</div>
                </div>
                <div class="search-box">
                    <div class="label">销毁pic的转账过期时间</div>
                    <div class="input-wrap">{{objData.picDestroyExpired}}</div>
                </div>
                <div class="search-box">
                    <div class="label">创建时间</div>
                    <div class="input-wrap">{{objData.createDate}}</div>
                </div>
                <div class="search-box">
                    <div class="label">更新时间</div>
                    <div class="input-wrap">{{objData.updateDate}}</div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
  import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        id: '',
        objData: Object,
        itemId: this.$route.query.itemId,
      }
    },
    created() {
      this.getDetail()
    },
    mounted() {
    },
    methods: {
      statusFn (length) {
        //状态 0:创建订单 1处理中 2:处理成功 3处理失败 4撤单
        let arr = ['创建订单','处理中','处理成功','处理失败','撤单']
        return arr[length]
      },
      callBackStateFn (length) {
        let arr = ['等待回调','回调中','回调成功','回调失败','停止回调','不需回调']
        return arr[length - 1]
      },
      confirmStateFn (length) {
        let arr = ['等待确认','确认成功','确认失败','停止确认','不需确认']
        return arr[length - 1]
      },
      picDestroyFlagFn (length) {
        let arr = ['待销毁','已销毁','不需销毁','销毁中']
        return arr[length - 1]
      },
      paymentTypeFn (length) {
        let arr = ['用户发布任务','给用户打款第一笔任务金','给用户打款第二笔任务金','任务卡更换任务','购买基金','赎回基金']
        return arr[length - 1]
      },
      getDetail () {
        this.$fetch.post(`/pay/onePay/preCatPayData?id=${this.itemId}`).then(res => {
            if (res.msg === '成功') {
              let objData = res.data.resultData
              for(let i in objData) {
                if (objData[i] === null || objData[i] === "") {
                  objData[i] = '-'
                }
              }
              this.objData = objData
            }
        })
      },
      // 重新回调
      theCallback () {
        Dialog({
          title: '确认重新回调？',
          okFn: () => {
            this.$fetch.post(`/pay/onePay/againCallback?bountyPayId=${this.objData.id}`,{
            }).then(res => {
              if (res.msg === '重新回调成功') {
                Dialog({
                  title: res.msg,
                  okFn: () => {
                    this.$router.push('/treasureManage/bountyPayRecord')
                  }
                })
              }
            })
          }
        })
      },
      //重新确认支付
      reconfirm () {
        Dialog({
          title: '重新确认支付？',
          okFn: () => {
            this.$fetch.post(`/pay/onePay/againConfirm?bountyPayId=${this.objData.id}`,{
            }).then(res => {
              if (res.msg === '重新确认支付成功') {
                Dialog({
                  title: res.msg,
                  okFn: () => {
                    this.$router.push('/treasureManage/bountyPayRecord')
                  }
                })
              }
            })
          }
        })
      },
      // 重新支付
      backPay () {
        Dialog({
          title: '确认重新支付？',
          okFn: () => {
            this.$fetch.post(`/pay/onePay/againPay?bountyPayId=${this.objData.id}`,{
            }).then(res => {
              if (res.msg === '重新支付成功') {
                Dialog({
                  title: res.msg,
                  okFn: () => {
                    this.$router.push('/treasureManage/bountyPayRecord')
                  }
                })
              }
            })
          }
        })
      }
    },
  
  }
</script>

<style lang="less" rel="stylesheet/less" scoped>
    @import "../../assets/css/var.less";
    .main-container {
        .wrap {
            .crumb{
                display: flex;
                align-items: center;
                justify-content: space-between;
                padding-bottom: 20px;
                color: #b7b9c1;
                .tit{
                    color: @text;
                }
                .preserve{
                    width: auto;
                    padding: 0 20px;
                    height: 34px;
                    line-height: 34px;
                    color: @white;
                    text-align: center;
                    background-color: #2176ff;
                    border-radius: 2px;
                    cursor: pointer;
                }
            }
            .main-content{
                background-color: @white;
                min-height: calc(100vh - 174px);
                padding: 24px 40px 0 24px;
                .search-box{
                    height: 30px;
                    line-height: 30px;
                    display: flex;
                    align-items: center;
                    justify-content: flex-start;
                    font-size: 14px;
                    color: @text;
                    margin: 0;
                    &:nth-child(1){
                        margin-top: 0;
                    }
                    &:nth-child(-n+4){
                        .input-wrap{
                            border: none;
                        }
                    }
                    .label{
                        width: 144px;
                        color: #b2b3bc;
                    }
                    .input-wrap{
                        margin-left: 40px;
                        width: auto;
                        height: 30px;
                        line-height: 30px;
                    }
                    .dropdown-wrap{
                        display: inline-block;
                        width: 300px !important;
                        height: 100% !important;
                        .el-dropdown{
                            width: 100%;
                            height: 100%;
                            line-height: 34px;
                            span{
                                display: flex;
                                align-items: center;
                                justify-content: space-between;
                            }
                        }
                    }
                }
                .quill-editor{
                    height: 434px;
                    position: relative;
                    .label{
                        width: 58px;
                    }
                    .editor{
                        position: absolute;
                        left: 98px;
                        top: 0;
                        width: 800px;
                        .ql-container{
                            height: calc(100% - 66px) !important;
                        }
                    }
                }
            }
        }
    }
</style>
