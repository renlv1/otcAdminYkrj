<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>后台转账日志</span>
    </div>
    <div class="page-wrap">
      <div class="search">
        <div class="search-box">
          <div class="label">ID：</div>
          <div class="input-wrap"><input v-model="valueID" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">用户名：</div>
          <div class="input-wrap"><input v-model.trim="userName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">币种：</div>
          <div class="input-wrap"><input v-model.trim="currency" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">转账状态：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="handleCommand2">
              <span class="el-dropdown-link">
                {{ statusText|| '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in stateArr" :key="index">{{item.text}}
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
            :picker-options="pickerOptionsEnd"
            placeholder="结束时间"
            v-model="endTime"
            default-time="23:59:59"
            type="datetime"
            format="yyyy-MM-dd HH:mm:ss"
          >
          </el-date-picker>
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
              <td>用户名</td>
              <td>用户地址</td>
              <td width="15%">收款地址</td>
              <td>转账金额</td>
              <td>手续费</td>
              <td width="15%">转账状态</td>
              <td>创建时间</td>
              <td>操作</td>
            </tr>
            <tr v-for="(item, index) in list" :key="index" class="tbody-tr">
              <td>{{item.id}}</td>
              <td>{{item.userName || '-'}}</td>
              <td>{{item.fromAddress}}</td>
              <td>{{item.toAddress}}</td>
              <td >{{item.amount}}</td>
              <td>{{item.serviceCharge}}</td>
              <td>{{$transferState(item.transferState)}}</td>
              <td v-html="timeStrDate(item.createAt)"></td>
              <td  class="blue-td">
                <router-link tag="a" :to="{path: '/user/backTransferLog/transferLogDetail', query: {id: item.id}}">查看</router-link>
              </td>
            </tr>
            <div style="z-index: 9999" class="web-loading1" v-show="loadShow">
              <img src="~@img/loading.gif" />
            </div>
          </table>
        </div>
      </list-wrap>
    </div>
  </div>
</template>

<script>
  // import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        valueID:'',
        userName: '',//账户用户名
        currency: '',//  币种
        stateArr: [],//转账状态
        statusText: '',
        startTime: '',
        endTime: '',
        transferState: '',//转账状态
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
        list: [],
        total: 0,
        pageIndex: 1,
        loadShow: false,
        pageSize: 10,
      }
    },
    created() {
      this.getType()
      // this.getData()
    },
    methods: {
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=user.ManagerTransferAccountLog$TransferStateEnum&flag=true&state=1`).then(res => {
          if (res.code === 0) {
            this.stateArr = res.data.data.TransferStateEnum
          }
        })
      },
      resetFields() {
        this.userName = ''
        this.startTime = ''
        this.startTime = ''
        this.endTime = ''
        this.currency = ''
        this.statusText = ''
        this.valueID = ''
        this.transferState= ''
      },
      handleCommand2(command) {
        this.statusText = command.text
        this.transferState = command.id
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/user/managerTransferAccountLog/queryManagerTransferAccountLogs', {
          id: this.valueID,
          userName: this.userName, //账户用户名
          currency: this.currency, //  币种
          transferState : this.transferState === 'selected' ? this.transferState = '' : this.transferState, //转账状态
          startDateStr: this.$changeDate(this.startTime, '-', 8), //
          endDateStr:  this.$changeDate(this.endTime, '-', 8), //
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize
        }).then((res) => {
          this.loadShow = false
          if(res.data) {
            this.list = res.data.page.list
            this.total = res.data.page.totalCount
          } else {
            this.list = []
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
  
  >>> .el-dialog .el-dialog__headerbtn .el-icon-close:before {
    border: 2px solid #ccc;
    border-radius: 100%;
  }
  
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
</style>
