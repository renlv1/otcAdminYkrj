<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>交易统计</span>
    </div>
    <div class="page-wrap">
      <div class="search">
        <div class="search-box">
          <div class="label">用户名：</div>
          <div class="input-wrap"><input v-model.trim="userName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">时间：</div>
          <el-date-picker
            placeholder="开始时间"
            v-model="startTime"
            type="datetime"
            :picker-options="pickerOptionsStart"
            format="yyyy-MM-dd HH:mm:ss"
            default-time="00:00:00"
          >
          </el-date-picker>

          <span style="margin-right: 20px">到</span>

          <el-date-picker
            placeholder="结束时间"
            v-model="endTime"
            type="datetime"
            :picker-options="pickerOptionsEnd"
            format="yyyy-MM-dd HH:mm:ss"
            default-time="23:59:59"
          >
          </el-date-picker>
        </div>
        <div class="f1"></div>
        <div class="btns">
          <div @click="resetFields()" class="btn black">清空</div>
          <div class="btn" @click="search">查询</div>
        </div>
      </div>
      <list-wrap :list="list" :total="total" @change="getTurn">
        <div class="web-table">
          <div class="center">
            <div class="common-thead">
              <div class="thead">描述</div>
              <div class="thead">数据1</div>
              <div class="thead tbody-address">数据2</div>
              <div class="thead">用户数</div>
            </div>
            <div v-for="(item, index) in isListNull(list)" :key="index" class="tbody-wrap">
              <div class="common-tbody">
                <div class="tbody">{{current(index)}}</div>
                <div class="tbody">{{item.depositAmount}}</div>
                <div class="tbody tbody-address">{{item.receiveAmount}}</div>
                <div class="tbody">{{item.userNumbers || 0}}</div>
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
        statusArr2: [],
        shareName: '',
        bossName2: '',
        username: '',
        bossName: '',
        userId: '',
        userName: '',
        statusArr: [],
        statusText: '',
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
        pageSize:10,
        loadShow: false,
        showDialog: false,
        userAddress: '',
        upperName: '',
        refAddress: '',
        currency: '',
        idValue: '',
        statusType: '',
        allList: [],
        page: 1,
      }
    },
    created() {
      // this.getData()
    },
    methods: {
      current(rowIndex) {
        if(this.page === 1) {
          if(rowIndex === 0){
            return '当天法币充值';
          }else if(rowIndex === 1){
            return '当天币币充值(转入)';
          }else if(rowIndex === 2){
            return '当天购买sie量';
          }else if(rowIndex === 3){
            return '当天购买达尔文量';
          }else if(rowIndex === 4){
            return '总法币充值';
          }else if(rowIndex === 5){
            return '总币币充值(转入)';
          }else if(rowIndex === 6){
            return '总购买sie量';
          }else if(rowIndex === 7){
            return '总购买达尔文量';
          }else if(rowIndex === 8){
            return '今日/总优惠值兑换sie';
          }else if(rowIndex === 9){
            return '今日新增用户/总用户';
          }else if(rowIndex === 10){
            return '交易所记录';
          }else if(rowIndex === 11){
            return '新交易所记录';
          }
        } else {
          if(rowIndex ==0){
            return '交易所记录';
          }else if(rowIndex ==1){
            return '新交易所记录';
          }
        }
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
      resetFields() {
        this.refAddress = ''
        this.upperName = ''
        this.statusText = ''
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
      // 状态
      handleCommand(command) {
        this.currencyText = command.text
        this.currency = command.id
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/trade/statistics/queryTradeStatistics', {
          username: this.userName, //
          startDateStr:this.$changeDate(this.startTime, '-', 8),
          endDateStr: this.$changeDate(this.endTime, '-', 8),
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize,
        }).then((res) => {
          this.loadShow = false
          if( res.data) {
            this.loadShow = false
            this.allList = res.data.depositList
            let allList = res.data.depositList
            this.list = allList.slice(0,this.pageSize)
            this.total = allList.length
          } else {
            this.list = []
            this.total = 0
          }
        }).catch(err => {
          this.loadShow = false
          console.log(err)
        })
      },
      getTurn(page) {
        this.page = page
        this.list = this.allList.slice((page - 1) * this.pageSize, page * this.pageSize,)
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
      border-radius: 2px;
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
        border-radius: 2px;
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
