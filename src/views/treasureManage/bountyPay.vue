<template>
    <div class="c-page mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>赏金单支付记录</span>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">发送用户名</div>
                        <div class="input-wrap"><input v-model.trim="sendUserName" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">接收用户名</div>
                        <div class="input-wrap"><input v-model.trim="receiveUserName" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">币种</div>
                        <div class="input-wrap"><input v-model.trim="currency" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">账单ID</div>
                        <div class="input-wrap"><input v-model="payTranId" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">数据来源ID</div>
                        <div class="input-wrap"><input v-model="sourceOrderId" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">支付方</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{payWhereText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 1)" v-for="(item,index) in payWhereEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{statusText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 2)" v-for="(item,index) in statusEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">回调状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{callBackText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 3)" v-for="(item,index) in callBackStateEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">支付类型</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{paymentTypeText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 4)" v-for="(item,index) in paymentTypeEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">支付确认状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{confirmStateText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 5)" v-for="(item,index) in confirmStateEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">PIC销毁标识</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{picDestroyText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 6)" v-for="(item,index) in picDestroyFlagEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="block">
                        <span class="demonstration">时间</span>
                        <el-date-picker
                            v-model="startDateStr"
                            type="datetime"
                            placeholder="开始时间"
                            :picker-options="pickerOptionsStart"
                            format="yyyy-MM-dd HH:mm:ss"
                            default-time="00:00:00">
                        </el-date-picker>
                        <span class="hr">-</span>
                        <el-date-picker
                            v-model="endDateStr"
                            type="datetime"
                            placeholder="结束时间"
                            :picker-options="pickerOptionsEnd"
                            format="yyyy-MM-dd HH:mm:ss"
                            default-time="23:59:59">
                        </el-date-picker>
                    </div>
                    <div class="search-box btns">
                        <div @click="resetFields()" class="btn black">清空</div>
                        <div class="btn" @click="search">查询</div>
                    </div>
                </div>
                <list-wrap :list="isListNull(list)" :total="total" @change="getData">
                    <div class="web-table">
                        <div class="center">
                            <div class="thead item-list">
                                <div class="li">ID</div>
                                <div class="li">发送用户名</div>
                                <div class="li">接收用户名</div>
                                <div class="li">支付金额</div>
                                <div class="li">币种</div>
                                <div class="li"><span>数据来源记录ID</span></div>
                                <div class="li"><span>支付类型</span></div>
                                <div class="li">支付方</div>
                                <div class="li">状态</div>
                                <div class="li">支付确认状态</div>
                                <div class="li">PIC销毁标识</div>
                                <div class="li">账单ID</div>
                                <div class="li">回调次数</div>
                                <div class="li">回调状态</div>
                                <div class="li">操作</div>
                            </div>
                            <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                                <div class="flex-item">
                                    <div class="li">{{item.id}}</div>
                                    <div class="li">{{item.sendUserName}}</div>
                                    <div class="li">{{item.receiveUserName}}</div>
                                    <div class="li">{{item.payAmount}}</div>
                                    <div class="li">{{item.currency}}</div>
                                    <div class="li"><span>{{item.sourceOrderId}}</span></div>
                                    <div class="li"><span>{{paymentTypeFn(item.paymentType)}}</span></div>
                                    <div class="li"><span v-if="item.payWhere ===1">本平台</span><span v-else-if="item.payWhere === 2">秘密平台</span></div>
                                    <div class="li">{{statusFn(item.status)}}</div>
                                    <div class="li">{{confirmStateFn(item.confirmState)}}</div>
                                    <div class="li">{{picDestroyFlagFn(item.picDestroyFlag)}}</div>
                                    <div class="li">{{item.payTranId}}</div>
                                    <div class="li">{{item.callBackTime}}</div>
                                    <div class="li">{{callBackStateFn(item.callBackState)}}</div>
                                    <div class="li operation">
                                        <router-link tag="a" class="in-btn" :to="{path: '/treasureManage/bountyPay/bountyPayRecordetails',query: {itemId: item.id}}">查看</router-link>
                                        <div class="in-btn" v-if="item.status === 2 && item.callBackState === 4" @click="theCallback(item.id)">重新回调</div>
                                        <div class="in-btn" v-else-if="((item.status === 1) && (item.confirmState === 2 || item.confirmState === 3))" @click="reconfirm(item.id)">重新确认支付状态</div>
                                        <div class="in-btn" v-else-if="item.status === 3" @click="backPay(item.id)">重新支付</div>
                                    </div>
                                </div>
                            </div>
                            <div style="z-index: 9999" class="web-loading1" v-show="loadShow">
                                <img src="~@img/loading.gif" />
                            </div>
                        </div>
                    </div>
                </list-wrap>
            </div>
        </div>
    </div>
</template>

<script>
  import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        pickerOptionsStart: {
          disabledDate: (time) => {
            let beginDateVal = this.endDateStr
            if (beginDateVal) {
              return time.getTime() > beginDateVal
            }
          }
        },
        pickerOptionsEnd: {
          disabledDate: (time) => {
            let beginDateVal = this.startDateStr
            if (beginDateVal) {
              return time.getTime() < beginDateVal
            }
          }
        },
        id: '',
        sendUserName:'',
        receiveUserName:'',
        currency: '',
        payTranId: '',
        sourceOrderId: '',
        list: [],
        total: 0,
        pageIndex: 1,
        loadShow: false,
        unfoldActive: 0,
        itemShow: true,
        callBackStateEnum: [],
        confirmStateEnum: [],
        payWhereEnum: [],
        paymentTypeEnum: [],
        picDestroyFlagEnum: [],
        statusEnum: [],
        payWhereText: '',
        payWhereId: '',
        statusText: '',
        statusId: '',
        callBackText: '',
        callBackId: '',
        paymentTypeText: '',
        paymentTypeId: '',
        confirmStateText: '',
        confirmStateId: '',
        picDestroyText: '',
        picDestroyId: '',
        startDateStr: '',
        endDateStr: '',
        userAddress: '',
      }
    },
    created () {
      this.getType()
    },
    methods: {
      statusFn (length) {
        let arr = ['创建订单','处理中','处理成功','处理失败','撤单']
        return arr[length]
      },
      callBackStateFn (length) {
        let arr = ['等待回调','回调中','回调成功','回调失败','停止回调','不需回调']
        return arr[length - 1]
      },
      paymentTypeFn (length) {
        let text = ''
        this.paymentTypeEnum.forEach(item => {
           if (parseInt(item.id) === parseInt(length)) {
             text = item.text
           }
        })
        return text
      },
      picDestroyFlagFn (length) {
        let text = ''
        this.picDestroyFlagEnum.forEach(item => {
          if (parseInt(item.id) === parseInt(length)) {
            text = item.text
          }
        })
        return text
      },
      confirmStateFn (length) {
        let text = ''
        this.confirmStateEnum.forEach(item => {
          if (parseInt(item.id) === parseInt(length)) {
            text = item.text
          }
        })
        return text
      },
      resetFields () {
        this.id = ''
        this.sendUserName = ''
        this.receiveUserName = ''
        this.currency = ''
        this.payTranId = ''
        this.sourceOrderId = ''
        this.payWhereId = ''
        this.statusId = ''
        this.callBackId = ''
        this.paymentTypeId = ''
        this.confirmStateId = ''
        this.picDestroyId = ''
        this.payWhereText = '全部'
        this.statusText = '全部'
        this.callBackText = '全部'
        this.paymentTypeText = '全部'
        this.confirmStateText = '全部'
        this.picDestroyText = '全部'
        this.startDateStr = ''
        this.endDateStr = ''
      },
      getType () {
        let url = '/public/enumValue?classPackage=pay.OnePayRecord$PayWhereEnum;pay.OnePayRecord$StatusEnum;pay.OnePayRecord$PaymentTypeEnum;pay.OnePayRecord$CallBackStateEnum;pay.OnePayRecord$ConfirmStateEnum;pay.OnePayRecord$PicDestroyFlagEnum&flag=true&state=4'
        this.$fetch.post(`${url}`).then(res => {
          if (res.msg === '成功') {
            this.callBackStateEnum = res.data.data.CallBackStateEnum // 回调转态
            this.confirmStateEnum = res.data.data.ConfirmStateEnum // 支付确认状态
            this.payWhereEnum =  res.data.data.PayWhereEnum //支付方
            this.paymentTypeEnum = res.data.data.PaymentTypeEnum // 支付类型
            this.picDestroyFlagEnum = res.data.data.PicDestroyFlagEnum  //PIC销毁
            this.statusEnum = res.data.data.StatusEnum  // 状态
          }
        })
      },
      commandChange (commend) {
        let num = commend.index
        if (num === 1) {
          this.payWhereText = commend.item.text
          this.payWhereId = commend.item.id
        } else if (num === 2) {
          this.statusText = commend.item.text
          this.statusId = commend.item.id
        } else if (num === 3) {
          this.callBackText = commend.item.text
          this.callBackId = commend.item.id
        } else if (num === 4) {
          this.paymentTypeText = commend.item.text
          this.paymentTypeId = commend.item.id
        } else if (num === 5) {
          this.confirmStateText = commend.item.text
          this.confirmStateId = commend.item.id
        } else if (num === 6) {
          this.picDestroyText = commend.item.text
          this.picDestroyId = commend.item.id
        }
      },
      search () {
        this.total = 1
        this.getData()
      },
      getData (pageIndex) {
        if (this.total !== 0) {
          this.loadShow = true
          let payWhere = this.payWhereId === 'selected' ? '' : this.payWhereId
          let status = this.statusId === 'selected' ? '' : this.statusId
          let callBackState = this.callBackId === 'selected' ? '' : this.callBackId
          let aymentType = this.paymentTypeId === 'selected' ? '' : this.paymentTypeId
          let confirmStatepic = this.confirmStateId === 'selected' ? '' : this.confirmStateId
          let picDestroyFlag = this.picDestroyId === 'selected' ? '' : this.picDestroyId
          if (this.total !== 0) {
            this.$fetch.post(`/pay/onePay/queryOnePayList`,{
              id: this.id,
              sendUserName: this.sendUserName,
              receiveUserName: this.receiveUserName,
              currency: this.currency,
              payTranId: this.payTranId,
              sourceOrderId: this.sourceOrderId,
              payWhere: payWhere,
              status: status,
              callBackState: callBackState,
              paymentType: aymentType,
              confirmState : confirmStatepic,
              picDestroyFlag : picDestroyFlag,
              startAtStr: this.$changeDate(this.startDateStr, '-', 8),
              endAtStr: this.$changeDate(this.endDateStr, '-', 8),
              pageIndex: pageIndex || this.pageIndex,
              pageSize: 10,
            }).then(res => {
              if (res.msg === '成功') {
                this.loadShow = false
                this.list = res.data.page.list
                this.total = res.data.page.totalCount
              }
            })
          }
        }
      },
      // 重新回调
      theCallback (id) {
        Dialog({
          title: '确认重新回调？',
          okFn: () => {
            this.$fetch.post(`/pay/onePay/againCallback?bountyPayId=${id}`,{
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
      reconfirm (id) {
        Dialog({
          title: '重新确认支付？',
          okFn: () => {
            this.$fetch.post(`/pay/onePay/againConfirm?bountyPayId=${id}`,{
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
      backPay (id) {
        Dialog({
          title: '确认重新支付？',
          okFn: () => {
            this.$fetch.post(`/pay/onePay/againPay?bountyPayId=${id}`,{
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
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
    .wrap
        .crumb
            padding-bottom 22px;
            margin-bottom 0;
        .main-content
            width 100%;
            background-color #fff;
            padding 0 40px 0 24px;
            min-height calc(100vh - 147px);
            .web-table
                padding-top 0
                .item-list
                    width 100%
                    height 64px
                    line-height 64px
                    display flex
                    align-items center
                    justify-content flex-start
                    &:nth-child(n+2)
                        display block
                        height auto
                        line-height normal
                        width 100%
                        .flex-item
                            width 100%
                            height 64px
                            line-height 64px
                            display flex
                            align-items center
                            justify-content flex-start
                            transition all .3s linear
                            border-left 1px solid #DCDFE6
                            border-right 1px solid #DCDFE6
                            border-bottom 1px solid #DCDFE6
                            &:hover
                                background-color rgba(0,0,0, .1)
                    &:nth-child(odd)
                        background-color #F8F8FA
                    .li
                        flex 1
                        line-height 15px
                        width auto
                    .li
                        text-align center
                    &:first-child
                        background-color #edeff2 !important;
                        color @text !important;
                    .operation
                        color #2176ff
                        cursor pointer
                        display flex
                        align-items center
                        justify-content center
                        .in-btn
                            border-right 1px solid #DCDFE6
                            padding-right 10px
                            &:nth-child(n+2)
                                padding-left 10px
                                &:hover
                                    color #121212
                            &:last-child
                                border-right none
                .table-list
                    height auto
    .search
        position relative
        display flex
        flex-direction row
        flex-wrap wrap
        justify-content flex-start
        border-bottom 0
        .search-box
            width 318px
            height 34px
            line-height 34px
            margin: 4px 0 15px;
            display flex
            align-items center
            &:nth-child(7)
                .label
                    width 130px
                .input-wrap
                    flex 1
            .label
                padding-right 16px
                font-size 14px
            .input-wrap
                flex 1
                height 34px
                margin-right 40px
                input
                    width 100%
                    height 100%
                    margin-right 0
                    border-radius 0
            .dropdown-wrap
                cursor pointer
                text-align center
                background-color #fff
                border: 1px solid #DCDFE6;
                border-radius: 0;
                padding: 0 10px;
                font-size 14px
                min-width: 130px !important;
                width: 200px !important;
                height: 34px !important;
                line-height: 34px !important;
                .el-dropdown
                    width 100%
                    height 100%
                    span
                        width 100%
                        display flex
                        align-items center
                        justify-content space-between
        .btns
            justify-content flex-end
            .btn
                cursor pointer

</style>
