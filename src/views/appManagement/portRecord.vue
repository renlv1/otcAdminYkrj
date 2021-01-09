<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>接口调用记录</span>
    </div>
    <div class="page-wrap">
      <div class="search">
        <div class="search-box">
          <div class="label">用户名：</div>
          <div class="input-wrap"><input v-model.trim="sendUserName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">用户地址：</div>
          <div class="input-wrap"><input v-model.trim="receiveUserName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">订单id：</div>
          <div class="input-wrap"><input v-model="userId" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">支付id：</div>
          <div class="input-wrap"><input v-model="sendAddress" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">类型：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="handleCommand">
              <span class="el-dropdown-link">
                {{tradetext || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in tradeArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="f1"></div>
        <div class="btns">
          <div @click="resetFields()" class="btn black">清空</div>
          <div class="btn" @click="search">查询</div>
        </div>
      </div>
      <list-wrap :list="list" :total="total" @change="getData">
        <div class="web-table">
          <div class="center">
            <div class="common-thead">
              <div class="thead">ID</div>
              <div class="thead">用户名</div>
              <div class="thead tbody-address">用户地址</div>
              <div class="thead">接口URL</div>
              <div class="thead">类型</div>
              <div class="thead">APP授权返回</div>
              <div class="thead">调接口参数</div>
              <div class="thead">返回参数</div>
              <div class="thead">状态</div>
              <div class="thead">操作</div>
            </div>
            <div v-for="(item, index) in isListNull(list)" :key="index" class="tbody-wrap">
              <div class=" common-tbody">
                <div class="tbody">{{item.id}}</div>
                <div class="tbody ">{{item.userName}}</div>
                <div class="tbody tbody-address  word-two more-text" @click="showTextDialog(item.userAddress,1)">{{item.userAddress}}</div>
                <div class="tbody word-two more-text" @click="showTextDialog(item.url,2)">{{item.url}}</div>
                <div class="tbody word-two" >{{$portType(item.type)}}</div>
                <div class="tbody word-two more-text" @click="showTextDialog(item.sessionid,3)">{{item.sessionid}}</div>
                <div class="tbody word-two more-text" @click="showTextDialog(JSON.parse(item.paramStr).trade_no,4)">{{paramStrData(item.paramStr)}}</div>
                <div class="tbody word-two more-text" @click="showTextDialog(item.returnStr,5)">{{item.returnStr}}</div>
                <div class="tbody">{{item.status === 'success' ? '成功' : '失败'}}</div>
                <div  @click="upload(item)" class="blue-td cursor tbody" v-show="item.packFlag === false"> <span>展开</span><img src="../../../src/assets/img/kai.png"></div>
                <div  @click="shouqi(item)" class="blue-td cursor tbody" v-show="item.packFlag === true"> <span>收起</span><img src="../../../src/assets/img/shou.png"></div>
              </div>
              <div class="add-more" v-show="item.packFlag === true">
                <div class="add-up">
                  <span>操作账户金额: {{item.total}}</span> <span>备注: {{item.remark}}</span> <span>订单id: {{item.orderid}}</span> <span>支付id: {{item.tradeno}}</span>
                  <span>创建时间: {{isEmptyText(item.createTime)}}</span> <span>更新时间: {{isEmptyText(item.updateTime)}}</span>
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
</template>

<script>
  import { Message } from 'element-ui'
  import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        pickerOptionsStart: {
          disabledDate: time => {
            if (this.endTime) {
              return time.getTime() > new Date(this.endTime).getTime()
            }
          }
        },
        pickerOptionsEnd: {
          disabledDate: time => {
            if (this.startTime) {
              return time.getTime() < new Date(this.startTime).getTime() - 86400000
            }
          }
        },
        pickerOptionsStart2: {
          disabledDate: time => {
            if (this.overTime) {
              return time.getTime() > new Date(this.overTime).getTime()
            }
          }
        },
        pickerOptionsEnd2: {
          disabledDate: time => {
            if (this.beginTime) {
              return time.getTime() < new Date(this.beginTime).getTime() - 86400000
            }
          }
        },
        sendUserName: '',
        sendAddress: '',
        receiveUserName: '',
        recAddress: '',
        callbackArr: [],
        confirmArr: [],
        confirmtext:'',
        tradetext: '',
        tradeArr: [],
        typeArr: [],
        shareName: '',
        bossName2: '',
        bossName: '',
        userId: '',
        userName: '',
        statusArr: [],
        statustext: '',
        callbacktext: '',
        transfertoBalance:'',//转到余额
        curren: '',//币种
        balanceEntrustFreeze: '',//委托冻结余额
        blockedBalanceAmout:'',//冻结余额
        balanceAmout: '',//余额
        accountUser: '',//账户用户名
        userDialog: false,
        beginTime: '',
        overTime: '',
        minAmount: '',
        curText: '',
        orderArr: [],
        currencyText: '',
        currencyArr: [],
        id: '',
        startTime: '',
        endTime: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        showDialog: false,
        userAddress: '',
        upperName: '',
        refAddress: '',
        currency: '',
        idValue: '',
        statusType: '',
        totaltext: '',
        changeType: '',
        txConfirmStatus:'',
        status: '',
        callbackState: '',
        addressDialog: false,
        textTitle:'',
      }
    },
    created() {
      this.getType()
      // this.getData()
    },
    methods: {
      showTextDialog(address,index){
        if (address) {
          let title = ''
          if(index === 1 ){
            title = '用户地址'
          } else if(index === 2) {
            title = '接口URL'
          } else if (index === 3)  {
            title = 'APP授权返回'
          } else if(index === 4) {
            title = '调接口参数'
          } else if (index === 5) {
            title = '返回参数'
          }
          Dialog ({
            title: title,
            content: address,
            type: 'confirm'
          })
        }
       
      },
      returnData(data) {
        let dataRu =  JSON.parse(data)
        return dataRu.msg
      },
      paramStrData(data){
        let datapar = JSON.parse(data)
        if(datapar.trade_no) return datapar.trade_no
        return '-'
      },
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=sdk.AppInterfaceLogForTrade$TypeEnum&flag=true&state=1`).then(res => {
          if (res.code === 0) {
            this.tradeArr = res.data.data.TypeEnum
          }
        })
      },
      orderStatus(status){
        this.statusArr.forEach(item => {
          if (Number(item.id) === Number(status)) {
            return item.text
          }
        })
      },
      handleCommand(command) {
        this.tradetext = command.text
        this.changeType = command.id
      },
      upload(item) {
        item.packFlag = !item.packFlag
      },
      shouqi(item) {
        item.packFlag = !item.packFlag
      },
      verify() {
        if(!this.username) {
          Message.error('请填写用户名')
          return
        }
        if(!this.currencyText || this.currencyText === 'selected') {
          Message.error('请选择状态')
          return
        }
        if(!this.shareName) {
          Message.error('请填写共享者名称')
          return
        }
        return true
      },
      // 新增
      paySure() {
        if(this.verify()) {
          this.$fetch.post('/finance/upServerBoss/updateUpServerBossInfo',{
            userName : this.userName,
            status: this.currency,
            bossName : this.shareName ,
          }).then(res => {
            if(res.code === 0) {
              Dialog({
                title: '',
                content: res.msg,
                okFn: () => {
                  this.getData()
                }
              })
              this.handleClose()
            } else {
              this.handleClose()
            }
          }).catch(err => {
            this.handleClose()
            console.log(err)
          })
        }
      },
      handleClose2() {
        this.userDialog = false
      },
      handleClose() {
        this.currencyText = ''
        this.beginTime = ''
        this.overTime = ''
        this.curText = ''
        this.showDialog = false
        this.currency = ''
      },
      addBtn() {
        this.shareName = ''
        this.username = ''
        this.endTime = ''
        this.minAmount = ''
        this.beginTime = ''
        this.overTime = ''
        this.curText = ''
        this.showDialog = true
        this.currency = ''
      },
      resetFields() {
        this.callbackState = ''
        this.callbacktext = ''
        this.txConfirmStatus= ''
        this.confirmtext = ''
        this.changeType = ''
        this.tradetext = ''
        this.recAddress = ''
        this.receiveUserName = ''
        this.sendAddress = ''
        this.sendUserName = ''
        this.totaltext = ''
        this.refAddress = ''
        this.upperName = ''
        this.statustext = ''
        this.statusType = ''
        this.bossName = ''
        this.userAddress = ''
        this.state = ''
        this.currencyText = ''
        this.currency = ''
        this.userId = ''
        this.userName = ''
        this.startTime = ''
        this.endTime = ''
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/sdk/interfaceLog/queryAppInterfaceLogList', {
          userName: this.sendUserName,
          userAddress: this.receiveUserName,
          orderid: this.userId,
          tradeno: this.sendAddress,
          type:this.changeType === 'selected' ? this.changeType = '' : this.changeType,
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize
        }).then((res) => {
          this.loadShow = false
          if( res.data) {
            this.loadShow = false
            let list = res.data.page.list
            list.forEach(item => {
              item.packFlag = false
            })
            this.list = list
            this.total = res.data.page.totalCount
          }
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
  .dialog-input{
    width 250px
  }
  .el-dropdown-menu{
    max-height 300px!important
    overflow auto !important
    /*max-width 200px!important*/
  }
  .payment{
    max-width 210px!important
  }
  .crumb
    display flex
    justify-content space-between
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
  
  >>> .el-dialog .el-dialog__headerbtn {
    top: 20px;
    right: 15px;
  }
  
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
</style>
