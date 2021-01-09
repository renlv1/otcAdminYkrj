<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>服务共享者</span>
      <div class="add-btn" @click="addBtn()"> + 新增 </div>
    </div>
    <div class="page-wrap">
      <div class="search">
        <div class="search-box">
          <div class="label">ID：</div>
          <div class="input-wrap"><input v-model="userId" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">用户名：</div>
          <div class="input-wrap"><input v-model.trim="userName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">共享者名称：</div>
          <div class="input-wrap"><input v-model.trim="bossName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">状态：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="handleCommand3">
              <span class="el-dropdown-link">
                {{ statusText|| '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in statusArr" :key="index">{{item.text}}
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
          <table class="center">
            <tr class="thead">
              <td>ID</td>
              <td>用户名</td>
              <td>用户地址</td>
              <td>状态</td>
              <td>共享者名称</td>
              <td>共享者地址</td>
              <td>创建时间</td>
              <td>更新时间</td>
              <td>操作</td>
            </tr>
            <tr v-for="(item, index) in isListNull(list)" :key="index" class="tbody-tr">
              <td>{{item.id}}</td>
              <td>{{item.userName}}</td>
              <td>{{item.userAddress}}</td>
              <td>{{$serverStatus(item.status)}}</td>
              <td >{{item.bossName}}</td>
              <td>{{item.bossAddress}}</td>
              <td v-html="timeStrDate(item.createAt)" class="nowrap"></td>
              <td v-html="timeStrDate(item.updateAt)" class="nowrap"></td>
              <td class="blue-td">
                <router-link tag="a" :to="{path: '/trade/upServerBoss/upServerBossDetail', query: {id: item.id}}">查看</router-link>
              </td>
            </tr>
            <div style="z-index: 9999" class="web-loading1" v-show="loadShow">
              <img src="~@img/loading.gif" />
            </div>
          </table>
        </div>
      </list-wrap>
      <el-dialog width="30%" :visible.sync="showDialog" :before-close="handleClose" title="新增老板币种支持">
        <div class="search textarea-main">
          <div class="search-box">
            <div class="label">用户名</div>
            <div class="input-wrap curreny-wrap"><input v-model.trim="username" type="text"></div>
          </div>
          <div class="search-box">
            <div class="label">状态</div>
            <div class="input-wrap curreny-wrap">
              <el-dropdown @command="handleCommand">
              <span class="el-dropdown-link">
                {{ currencyText|| '未选择'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
                <el-dropdown-menu class="menu dialog-input" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in statusArr2" :key="index">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </div>
          <div class="search-box">
            <div class="label">共享者名称</div>
            <div class="input-wrap curreny-wrap "><input v-model.trim="shareName" type="text"></div>
          </div>
        </div>
        <span slot="footer" class="dialog-footer">
        <el-button @click="handleClose()">取 消</el-button>
        <el-button type="primary" @click="paySure()">确 定</el-button>
      </span>
      </el-dialog>
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
        loadShow: false,
        showDialog: false,
        userAddress: '',
        currency: '',
        idValue: '',
        statusType: '',
        pageSize: 10,
      }
    },
    created() {
      this.getType()
      this.getType2()
      // this.getData()
    },
    methods: {
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=finance.UpServeBoss$StatusEnum&flag=true&state=1`).then(res => {
          if (res.code === 0) {
            this.statusArr = res.data.data.StatusEnum
          }
        })
      },
      getType2() {
        this.$fetch.get(`/public/enumValue?classPackage=finance.UpServeBoss$StatusEnum&flag=false&state=1`).then(res => {
          if (res.code === 0) {
            this.statusArr2 = res.data.data.StatusEnum
          }
        })
      },
      //用户类型
      handleCommand3(command) {
        this.statusText = command.text
        this.statusType = command.id
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
            userName : this.username,
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
        this.statusText = ''
        this.statusType = ''
        this.bossName = ''
        this.userAddress = ''
        this.state = ''
        this.currencyText = ''
        this.currency = ''
        this.userId = ''
        this.userName = ''
        this.userAddress = ''
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
        this.$fetch.post('/finance/upServerBoss/queryUpServerBossList', {
          id: this.userId,
          userName: this.userName, //
          userAddress: this.userAddress,
          bossName: this.bossName,
          status: this.statusType === 'selected' ? this.statusType = '' : this.statusType,
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize
        }).then((res) => {
          this.loadShow = false
          if( res.data) {
            this.list = res.data.page.list
            this.total = res.data.page.totalCount
          } else {
            this.list = []
            this.total = 0
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
    align-items center
    justify-content space-between
    margin-bottom: 20px;
    margin-top: -13px;
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
