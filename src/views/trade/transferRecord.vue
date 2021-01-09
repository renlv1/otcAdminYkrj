<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>转账记录</span>
      <div class="add-btn-w">
        <span class="add-btn" @click="generalLedger()">查看总账</span>
        <span class="add-btn" @click="exchangeBtn()">交易所退款</span>
        <span class="add-btn" @click="failureBtn()">解冻失败退款</span>
      </div>
    </div>
    <div class="page-wrap">
      <div class="search">
        <div class="search-box">
          <div class="label">ID：</div>
          <div class="input-wrap"><input v-model="valueID" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">发送者用户名：</div>
          <div class="input-wrap"><input v-model.trim="sendUserName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">发送者用户地址：</div>
          <div class="input-wrap"><input v-model.trim="sendAddress" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">接收者用户名：</div>
          <div class="input-wrap"><input v-model.trim="receiveUserName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">接收者用户地址：</div>
          <div class="input-wrap"><input v-model.trim="receiveUserAddress" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">订单号：</div>
          <div class="input-wrap"><input v-model="orderNo" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">订单状态：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{ orderText|| '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 1)" v-for="(item, index) in orderArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">汇总状态：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{summaryText|| '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 2)" v-for="(item, index) in summaryArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">币种：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{ currencyText|| '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 3)" v-for="(item, index) in currencyArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
        <div class="label">是否累计业绩：</div>
        <div class="input-wrap dropdown-wrap">
          <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{ IsTotalText|| '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
            <el-dropdown-menu class="menu" slot="dropdown">
              <el-dropdown-item :command="composeValue(item, 4)" v-for="(item, index) in IsTotalArr" :key="index">{{item.text}}
              </el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </div>
      </div>
        <div class="search-box">
          <div class="label">数据类型：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{  RecordTypeText|| '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 5)" v-for="(item, index) in  RecordTypeArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">时间：</div>
          <el-date-picker
            placeholder="开始时间"
            v-model="startTime"
            type="datetime"
            default-time="00:00:00"
            :picker-options="pickerOptionsStart"
            format="yyyy-MM-dd HH:mm:ss"
          >
          </el-date-picker>

          <span style="margin-right: 10px">到</span>

          <el-date-picker
            placeholder="结束时间"
            v-model="endTime"
            type="datetime"
            default-time="23:59:59"
            :picker-options="pickerOptionsEnd"
            format="yyyy-MM-dd HH:mm:ss"
          >
          </el-date-picker>
        </div>
        <div class="search-box">
          <div class="label">购买SIE类型：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{TradeTypeText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 6)" v-for="(item, index) in TradeTypeArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">hash值：</div>
          <div class="input-wrap"><input v-model="orderHashs" type="text"></div>
        </div>
        <div class="f1"></div>
        <div class="btns">
          <div @click="resetFields()" class="btn black">清空</div>
          <div class="btn" @click="search">查询</div>
        </div>
      </div>
      <list-wrap :list="list" :total="total" @change="getData">
        <div class="web-table">
          <table class="center">
            <tr class="thead">
              <td width="6%">ID</td>
              <td>发送者用户名</td>
              <td width="15%">接受者用户名</td>
              <td>订单状态</td>
              <td>数据类型</td>
              <td>币种</td>
              <td>输入金额</td>
              <td>实际获得金额</td>
              <td>单价</td>
              <td width="15%">操作</td>
            </tr>
            <tr v-for="(item, index) in isListNull(list)" :key="index" class="tbody-tr">
              <td>{{item.id}}</td>
              <td>{{item.sendUserName}}</td>
              <td>{{item.receiveUserName}}</td>
              <td>{{$tradeStatus(item.orderStatus || '-')}}</td>
              <td>{{$recordType(item.recordType || '-')}}</td>
              <td>{{item.sendCurrency || '-'}}</td>
              <td>{{item.enterPrice}}</td>
              <td>{{item.actualPrice}}</td>
              <td>{{item.unitPrice}}</td>
              <td class="blue-td">
                <router-link tag="a" :to="{path: '/trade/transferRecord/transferRecordDetail',query: {id:item.id}}">查看 </router-link>
                <span @click="modiftyBtn(item)" v-show="item.checkCount>=6 && item.orderStatus==12 && item.recordType ==2"> <span class="grey" > | </span> 重新转账  </span>
                <router-link tag="a" :to="{path: '/trade/transferRecord/transferAudit',query: {id: item.id}}" v-show="item.orderStatus == 18 && (item.recordType == 1 || item.recordType == 8)"> <span class="grey" > | </span> 转入审核  </router-link>
                <router-link tag="a" :to="{path: '/trade/transferRecord/transferAudit',query: {id: item.id}}"  v-show="item.orderStatus == 18 "> <span class="grey" > | </span> 审核  </router-link>
              </td>
            </tr>
              <div class="ajax-loading" v-show="loadShow">
                  <div class="loading-content">
                      <img src="@img/loading2.gif" alt="">
                      <p>正在加载中...</p>
                  </div>
              </div>
          </table>
        </div>
      </list-wrap>
    </div>
    <el-dialog width="30%" :visible.sync="showLedgerDialog" :before-close="handleLedgerClose" title="查看总账">
        <div class="search">
            <div class="search-box">
                <div class="label">用户名</div>
                <div class="input-wrap"><input v-model.trim="accountName" type="text"></div>
            </div>
        </div>
        <span slot="footer" class="dialog-footer">
        <el-button @click="handleLedgerClose()">取 消</el-button>
        <el-button type="primary" @click="confirmLedger()">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog width="30%" :visible.sync="showDialog" :before-close="handleClose" title="交易所退款">
      <div class="search">
        <div class="search-box">
          <div class="label">退款id</div>
          <div class="input-wrap"><input v-model="refundID" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">类型</div>
          <div class="input-wrap curreny-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{curText}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu dialog-input" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 7)" v-for="(item, index) in exchangeArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="handleClose()">取 消</el-button>
        <el-button type="primary" @click="exchengeSure()">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog width="30%" :visible.sync="contactInformationDialog" :before-close="handleClose" title="解冻失败退款">
      <div class="search">
        <div class="search-box">
          <div class="label">解冻失败退款id</div>
          <div class="input-wrap input-main"><input v-model="failID" type="text"></div>
        </div>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="contactInformationDialog = false">取 消</el-button>
        <el-button type="primary" @click="confirmBtn()">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog width="30%" :visible.sync="transferDialog" :before-close="handleClose" title="重新转账">
      <div class="problem-box">
        <img src="../../assets/img/warning.png"><label>确定重新转账？</label>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="transferDialog = false">取 消</el-button>
        <el-button type="primary" @click="transferBtn()">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  import { Message } from 'element-ui'
  import Dialog from '@/components/dialog/dialog'

  export default {
    data() {
      return {
        pickerOptionsStart: {
          disabledDate: (time) => {
            let beginDateVal = this.endTime
            if (beginDateVal) {
              return time.getTime() > beginDateVal
            }
          }
        },
        pickerOptionsEnd: {
          disabledDate: (time) => {
            let beginDateVal = this.startTime
            if (beginDateVal) {
              return time.getTime() < beginDateVal
            }
          }
        },
        exchangeArr: [
          {id: 1,text: '传统交易所'}
        ],
        transferDialog:false,
        refundID: '',
        curText: '传统交易所',
        failID: '',
        TradeTypeArr: [],
        TradeTypeText: '',
        RecordTypeArr: [],
        RecordTypeText: '',
        IsTotalArr: [],
        IsTotalText: '',
        currencyArr: [],
        currencyText: '',
        summaryArr: [],
        summaryText: '',
        orderArr: [],
        orderText: '',
        orderHashs: '',
        valueID: '',
        sendUserName: '',
        sendAddress: '',
        receiveUserName: '',
        receiveUserAddress: '',
        orderNo: '',
        userName: '',//用户名
        accountAddress: '',//用户地址
        refUserName: '',//上级用户名
        refAddress: '',//上级用户地址
        mobile: '',//手机号
        email: '',//邮箱
        statusArr: [],//账户状态
        statusText: '',
        contactInformationDialog: false,//修改用户上级弹窗
        userText: '',
        userArr: [],//用户类型
        systemAddress: '',//以太坊地址
        startTime: '',
        endTime: '',
        status: '',//账户状态
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        showDialog: false,//确定注销弹窗
        showLedgerDialog: false,//确定注销弹窗
        userId: '',//用户id
        parentUserName: '',//上级用户名
        userType: '',//用户类型
        deleteId: '',
        orderStatus: '',
        summaryStatus: '',
        sendCurrency: '',
        isTotal: '',
        tradeType: '',
        recordType: '',
        type: 1,
        checkCount: '',
        transferId: '',
        accountName: ''
      }
    },
    created() {
      this.getType()
      // this.getData()
    },
    methods: {
      exchengeSure() {
        this.$fetch.post('/trade/transferRecord/unFreezeBalance',{
          trustId: this.refundID,
          type:this.type
        }).then(res => {
          if(res.code === 0) {
            this.showDialog = false
            Dialog({
              title: '',
              content: res.msg,
            })
          }
        })
      },
      // 查看总账
      generalLedger () {
        this.showLedgerDialog = true
      },
      handleLedgerClose () {
        this.showLedgerDialog = false
        this.accountName = ''
      },
      //跳查看总账
      confirmLedger () {
        if (this.accountName === '') {
          Message.error('用户名不能为空!')
        } else {
          this.loadShow = true
          this.$fetch.post(`/trade/transferRecord/preCheckAuditInfo?userName=${this.accountName}`).then(res => {
            if (res.code === 0) {
              let ledger = res.data
              if (ledger) {
                this.ledger = ledger
                this.loadShow = false
                this.$router.push({path: '/trade/transferRecord/generalLedger'})
                this.$store.commit('SET_LEDGER',ledger)
              } else {
                if (ledger === null || ledger === '') {
                  Message.error('暂无数据!')
                  this.loadShow = false
                }
              }
            } else {
              Message.error(res.msg)
            }
          })
        }
      },
      //交易所退款
      exchangeBtn(){
        this.showDialog = true
      },
      confirmBtn() {
        this.$fetch.post('/trade/transferRecord/refundTotalForFail',{
          id: this.failID
        }).then(res => {
          if(res.code === 0) {
            this.contactInformationDialog = false
            Dialog({
              title: '',
              content: res.msg,
            })
          }
        })
      },
      //解冻失败退款
      failureBtn(){
        this.contactInformationDialog = true
      },
      // 确定注销
      paySure() {
        this.$fetch.post(`/user/userAction/cancellation`,{
          id: this.deleteId
        }).then(res => {
          if(res.code === 0) {
            this.showDialog = false
            Dialog({
              title: '',
              content: res.msg,
              okFn: () => {
                this.getData(1)
              }
            })
          }
        }).catch(err => {
          console.log(err)
        })
      },
      // 注销
      cancelBtn(id) {
        this.deleteId = id
        this.showDialog = true
      },
      // 确定修改
      modifSure() {
        if(!this.userId) {
          Message.error('请填写用户Id')
          return
        }
        if(!this.parentUserName) {
          Message.error('请填写上级用户名')
          return
        }
        this.$fetch.post('/user/userAction/updateUserLevel', {
          userId: this.userId,//用户id
          refUserName: this.parentUserName//上级用户名
        }).then(res => {
          if(res.code === 0) {
            this.contactInformationDialog = false
            Dialog({
              title: '',
              content: res.msg,
              okFn: () => {
                this.getData(1)
              }
            })
          }
        }).catch(err => {
          console.log(err)
        })
      },
      // 重新转账按钮
      modiftyBtn(item){
        this.transferId = item.id
        this.checkCount = item.checkCount
        this.transferDialog = true
      },
      //重新转账
      transferBtn(){
        this.$fetch.post(`/trade/transferRecord/updateTransferRecord`,{
          id: this.transferId,
          checkCount: this.checkCount,
        }).then(res => {
          if(res.code === 0) {
            this.transferDialog = false
            Dialog({
              title: '',
              content: res.msg,
            })
          }
        }).catch(err => {
          console.log(err)
        })
      },
      // 修改用户上级
      handleClose() {
        this.failID = ''
        this.refundID = ''
        this.parentUserName = ''
        this.showDialog = false
        this.contactInformationDialog = false
      },
      // 枚举
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=trade.TransferRecord$RecordTypeEnum;trade.TransferRecord$CurrencyTypeEnum;trade.TransferRecord$IsTotalEnum;trade.TransferRecord$OrderStatusEnum;trade.TransferRecord$SummaryStatusEnum;trade.TransferRecord$TradeTypeEnum&flag=true&state=1`).then(res => {
          if (res.code === 0) {
            this.orderArr = res.data.data.OrderStatusEnum
            this.summaryArr = res.data.data.SummaryStatusEnum
            this.currencyArr = res.data.data.CurrencyTypeEnum
            this.IsTotalArr = res.data.data.IsTotalEnum
            this.RecordTypeArr = res.data.data.RecordTypeEnum
            this.TradeTypeArr = res.data.data.TradeTypeEnum
          }
        })
      },
      // 重置
      resetFields() {
        this.recordType = ''
        this.TradeTypeText = ''
        this.RecordTypeText = ''
        this.tradeType = ''
        this.IsTotalText = ''
        this.isTotal = ''
        this.currencyText = ''
        this.sendCurrency = ''
        this.summaryText = ''
        this.summaryStatus = ''
        this.orderText = ''
        this.orderStatus = ''
        this.orderHashs = ''
        this.userText = ''
        this.statusText= ''
        this.refAddress = ''
        this.refUserName = ''
        this.userName = ''
        this.accountAddress = ''
        this.email = ''
        this.startTime = ''
        this.mobile = ''
        this.userType = ''
        this.endTime = ''
        this.systemAddress = ''
        this.status = ''
        this.valueID = ''
        this.sendUserName = ''
        this.sendAddress = ''
        this.receiveUserName = ''
        this.receiveUserAddress = ''
        this.orderNo = ''
      },
      commandChange (commend) {
        let item = commend.item
        let num = commend.index
        if (num === 1) {
          this.orderText = item.text
          this.orderStatus = item.id
        } else if (num === 2) {
          this.summaryText = item.text
          this.summaryStatus = item.id
        } else if (num === 3) {
          this.currencyText = item.text
          this.sendCurrency = item.id
        } else if (num === 4) {
          this.IsTotalText = item.text
          this.isTotal = item.id
        } else  if (num === 5) {
          this.RecordTypeText = item.text
          this.recordType = item.id
        } else if (num === 6) {
          this.TradeTypeText = item.text
          this.tradeType = item.id
        } else if (num === 7) {
          this.curText = item.text
          this.type = item.id
        }
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/trade/transferRecord/queryTransferRecordList', {
          id: this.valueID,
          sendUserName: this.sendUserName,
          sendAddress: this.sendAddress,
          receiveUserName: this.receiveUserName,
          receiveUserAddress: this.receiveUserAddress,
          orderNo:this.orderNo,
          orderStatus:this.orderStatus === 'selected' ? this.orderStatus = '' : this.orderStatus, // 订单状态
          summaryStatus: this.summaryStatus === 'selected' ? this.summaryStatus = '' : this.summaryStatus, // 汇总状态
          sendCurrency: this.sendCurrency === 'selected' ? this.sendCurrency = '' : this.sendCurrency,//币种
          isTotal: this.isTotal === 'selected' ? this.isTotal = '' : this.isTotal,//是否累计业绩
          tradeType: this.tradeType === 'selected' ? this.tradeType = '' : this.tradeType, //  购买SIE类型
          recordType: this.recordType === 'selected' ? this.recordType = '' : this.recordType, //  数据类型
          startDateStr: this.$changeDate(this.startTime, '-', 8),//创建-结束时间
          endDateStr:  this.$changeDate(this.endTime, '-', 8),
          orderHashs: this.orderHashs,//hash值
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize
        }).then((res) => {
          this.loadShow = false
          this.list = res.data.page.list
          this.total = res.data.page.totalCount
        }).catch(err => {
          this.loadShow = false
          console.log(err)
        })
      },
      search() {
        this.total = 1 // hook listWrap组件的loading
        this.getData()
      },
    },

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
  .dialog-input{
    width 300px!important
  }
  .problem-box{
    margin-top 30px
    margin-bottom 40px
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
    position relative
    display flex
    flex-direction row
    flex-wrap wrap
    .search-box
      margin-top 30px
      margin-bottom 0
      display flex
      flex-direction row
      align-items center
      .dropdown-wrap
        cursor pointer
        text-align center
        line-height 40px
        width: 180px;
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
</style>
