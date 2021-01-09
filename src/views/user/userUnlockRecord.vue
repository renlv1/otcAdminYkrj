<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>账单解锁记录</span>
      <div class="btn-right">
        <span class="pay-money" @click="assignUnlock()">指定解锁</span>
        <span class="pay-money" @click="allUnlock">全部解锁</span>
      </div>
    </div>
    <div class="page-wrap">
      <div class="search">
        <div class="search-box">
          <div class="label">ID：</div>
          <div class="input-wrap"><input v-model="id" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">用户地址：</div>
          <div class="input-wrap"><input v-model.trim="userAddress" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">币种：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="handleCommand">
              <span class="el-dropdown-link">
                {{ currencyText|| '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in currencyArr" :key="index">{{item.text}}
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
              <td class="tboby-cont" width="20%">用户地址</td>
              <td>解锁总金额</td>
              <td>本次解锁金额</td>
              <td>解锁后剩余金额</td>
              <td>解锁时间</td>
              <td>状态</td>
              <td>创建时间</td>
              <td>更新时间</td>
            </tr>
            <tr v-for="(item, index) in list" :key="index" class="tbody-tr">
              <td>{{item.id}}</td>
              <td class="tboby-cont">{{item.userAddress}}</td>
              <td>{{item.txTotalAmount || '0'}}</td>
              <td>{{item.unlockAmount || '0'}}</td>
              <td>{{item.surplusAmount || '0'}}</td>
              <td>{{$changeDate(item.unlockTime)}}</td>
              <td>{{$unlockStatus(item.status)}}</td>
              <td v-html="timeStrDate(item.createTime)"></td>
              <td v-html="timeStrDate(item.updateTime)"></td>
            </tr>
            <div style="z-index: 9999" class="web-loading1" v-show="loadShow">
              <img src="~@img/loading.gif" />
            </div>
          </table>
        </div>
      </list-wrap>
      <el-dialog width="30%" :visible.sync="showDialog" :before-close="handleClose" title="全部解锁">
        <div class="search">
          <div class="search-box">
            <div class="label">日期</div>
            <el-date-picker
              v-model="beginTime"
              prefix-icon="el-icon-date"
              type="datetime"
              value-format="yyyy-MM-dd HH:mm:ss"
            >
            </el-date-picker>
          </div>
          <div class="search-box">
            <div class="label">总期数</div>
            <div class="input-wrap"><input v-model="unlockRuleCountPeriod" type="number"></div>
          </div>
          <div class="search-box">
            <div class="label">周期</div>
            <div class="input-wrap"><input v-model="unlockRulePeriod" type="number"></div>
          </div>
        </div>
        <span slot="footer" class="dialog-footer">
        <el-button @click="handleClose()">取 消</el-button>
        <el-button type="primary" @click="paySure()">确 定</el-button>
      </span>
      </el-dialog>
      <el-dialog width="30%" :visible.sync="userDialog" :before-close="handleClose2" title="指定解锁">
        <div class="search">
          <div class="search-box">
            <div class="label">用户地址</div>
            <div class="input-wrap input-main"><input v-model="accountUser" type="text"></div>
          </div>
          <div class="search-box">
            <div class="label">金额</div>
            <div class="input-wrap input-main"><input v-model="balanceAmout" type="text"></div>
          </div>
          <div class="search-box">
            <div class="label">日期</div>
            <el-date-picker
              v-model="endTime"
              prefix-icon="el-icon-date"
              type="datetime"
              value-format="yyyy-MM-dd HH:mm:ss"
            >
            </el-date-picker>
          </div>
          <div class="search-box">
            <div class="label">总期数</div>
            <div class="input-wrap input-main"><input v-model="unlockRuleCountPeriod" type="text"></div>
          </div>
          <div class="search-box">
            <div class="label">周期</div>
            <div class="input-wrap input-main"><input v-model="unlockRulePeriod" type="text"></div>
          </div>
        </div>
        <span slot="footer" class="dialog-footer">
        <el-button @click="handleClose2()">取 消</el-button>
        <el-button type="primary" @click="modifSure()">确 定</el-button>
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
        id: '',
        userAddress: '',
        currencyText: '',
        currencyArr: [],
        beginTime: '',
        userDialog: false,
        unlockRuleCountPeriod: '',
        unlockRulePeriod: '',
        balanceAmout: '',
        accountUser: '',
        endTime: '',
        list: [],
        total: 0,
        pageIndex: 1,
        loadShow: false,
        showDialog: false,
        currency: '',
        pageSize: 10,
      }
    },
    created() {
      this.getType()
      // this.getData()
    },
    methods: {
      // 全部解锁
      allUnlock() {
        this.showDialog = true
      },
      // 指定解锁
      assignUnlock(){
        this.userDialog = true
      },
      //枚举
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=user.UserAccountUnlockLog$StatusEnum&flag=true&state=1`).then(res => {
          if (res.code === 0) {
            this.currencyArr = res.data.data.StatusEnum
          }
        })
      },
      verify() {
        if(!this.beginTime) {
          Message.error('请选择日期')
          return
        }
        if(!this.unlockRuleCountPeriod) {
          Message.error('请填写总期数')
          return
        }
        if(!this.unlockRulePeriod) {
          Message.error('请填写周期')
          return
        }
        return true
      },
      verifyAssign() {
        if(!this.accountUser) {
          Message.error('请填写用户地址')
          return
        }
        if(!this.balanceAmout) {
          Message.error('请填写金额')
          return
        }
        if(!this.endTime) {
          Message.error('请填写日期')
          return
        }
        if(!this.unlockRuleCountPeriod) {
          Message.error('请填写总期数')
          return
        }
        if(!this.unlockRulePeriod) {
          Message.error('请填写周期')
          return
        }
        return true
      },
      // 确定指定解锁
      modifSure() {
        if(this.verifyAssign()) {
          this.$fetch.post('/user/userUnlock/assignUserUnlockOperation',{
            userAddress : this.accountUser,// 用户地址
            amount: this.balanceAmout,//总金额
            date:  this.endTime,//解锁开始计算时间
            unlockRuleCountPeriod: this.unlockRuleCountPeriod,//总期数
            unlockRulePeriod: this.unlockRulePeriod ,//周期
          }).then(res => {
            if(res.code === 0) {
              this.handleClose2()
              this.userDialog = false
              Dialog({
                title: '',
                content: res.msg,
                okFn: ( ) => {
                  this.getData()
                }
              })
            } else {
              this.handleClose2()
            }
          }).catch(err => {
            this.handleClose2()
            console.log(err)
          })
        }
      },
      // 确定全部解锁
      paySure() {
        if(this.verify()) {
          this.$fetch.post('/user/userUnlock/allUserUnlockOperation',{
            date: this.beginTime,
            unlockRuleCountPeriod: this.unlockRuleCountPeriod,
            unlockRulePeriod: this.unlockRulePeriod,
          }).then(res => {
            if(res.code === 0) {
              this.handleClose()
              Dialog({
                title: '',
                content: res.msg,
                okFn: () => {
                 this.getData()
                }
              })
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
        this.balanceAmout = ''
        this.accountUser = ''
        this.unlockRulePeriod = ''
        this.endTime = ''
        this.unlockRuleCountPeriod  = ''
        this.userDialog = false
      },
      handleClose() {
        this.unlockRulePeriod = ''
        this.beginTime = ''
        this.unlockRuleCountPeriod  = ''
        this.showDialog = false
      },
      resetFields() {
        this.currencyText = ''
        this.currency = ''
        this.id = ''
        this.userAddress = ''
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
        this.$fetch.post('/user/userUnlock/queryUserUnlockList', {
          id: this.id,
          userAddress: this.userAddress,
          status: this.currency === 'selected' ? this.currency = '' : this.currency ,
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize
        }).then((res) => {
          this.loadShow = false
          if( res.data) {
            this.list = res.data.page.list
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
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
  .page-wrap{
    margin-top 40px
  }
  .btn-right{
    top 25px
    right 20px
  }
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
          width 70%!important
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
          width 70%
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
