<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb detail-page">
      <div class="title-left">
        <div class="title-m">评估用户none</div>
      </div>
      <div class="btn-right">
        <span class="btn-save" @click="sureBtn()">查询</span>
      </div>
    </div>
    <div class="page-wrap detail-cont">
      <ul class="ul-w">
        <li class="li-item search">
          <div class="search-box">
            <div class="label">用户名</div>
            <div class="input-wrap"><input v-model="userName"></div>
          </div>
        </li>
        <div v-show="dataShow" class="bg-fff">
          <li class="li-item search">
            <div class="search-box">
              <div class="label">是否None正常</div>
              <div class="input-wrap">{{dataDetail.noneNormal}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">总藏宝量</div>
              <div class="input-wrap">{{dataDetail.initTotal}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">首次藏宝时间</div>
              <div class="input-wrap">{{$changeDate(dataDetail.firstInitPayDateStr)}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">已解锁张数</div>
              <div class="input-wrap">{{dataDetail.unlockMapCount}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">总分图价值</div>
              <div class="input-wrap">{{dataDetail.assignMapTotal}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">用户名</div>
              <div class="input-wrap">{{dataDetail.userName}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">总分图量</div>
              <div class="input-wrap">{{dataDetail.assignTotal}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">总加速天数</div>
              <div class="input-wrap">{{dataDetail.expeditDayTotal}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">总挖宝量</div>
              <div class="input-wrap">{{dataDetail.payToUserTotal}}</div>
            </div>
          </li>
        </div>
        <div v-show="!dataShow && inEmpty" class="bg-fff">
          <li class="li-item search">
            <div class="search-box">
              <div class="label">是否None正常</div>
              <div class="input-wrap">{{dataDetail.noneNormal || '-'}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">总藏宝量</div>
              <div class="input-wrap">{{dataDetail.initTotal || '-'}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">首次藏宝时间</div>
              <div class="input-wrap">{{$changeDate(dataDetail.firstInitPayDateStr) || '-'}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">已解锁张数</div>
              <div class="input-wrap">{{dataDetail.unlockMapCount || '-'}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">总分图价值</div>
              <div class="input-wrap">{{dataDetail.assignMapTotal || '-'}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">用户名</div>
              <div class="input-wrap">{{dataDetail.userName || '-'}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">总分图量</div>
              <div class="input-wrap">{{dataDetail.assignTotal || '-'}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">总加速天数</div>
              <div class="input-wrap">{{dataDetail.expeditDayTotal || '-'}}</div>
            </div>
          </li>
          <li class="li-item search">
            <div class="search-box">
              <div class="label">总挖宝量</div>
              <div class="input-wrap">{{dataDetail.payToUserTotal || '-'}}</div>
            </div>
          </li>
        </div>
      </ul>
    </div>
    <div style="z-index: 9999" class="web-loading1" v-show="loadShow">
      <img src="~@img/loading.gif" />
    </div>
  </div>
</template>

<script>
  import Dialog from '@/components/dialog/dialog'
  import { Message } from 'element-ui'
  export default {
    name: "transferLogDetail",
    data() {
      return{
        inEmpty: false,
        userName: '',
        loadShow:false,
        dataShow:false,
        dataDetail: {},
        openfireArr: [
          {id: 0, text: '未注册'},
          {id: 1, text: '已注册'}
        ],//是否注册openfire用户
        synStatusArr: [
          {id: 0, text: '未注册'},
          {id: 1, text: '已注册'}
        ],//注册数据同步状态
        synStatusText: '',
        openfireText: '',
        openfire: '',//是否注册openfire用户
        genderText:  '',
        genderArr: [],//性别
        gender: '',
        statusArr: [],//账户状态
        statusText: '',
        tradeArr: [],//支付密码
        tradeText: '',
        mobileArr: [],//手机验证码
        mobileText: '',
        googleArr: [],//Gogole验证码
        googleText: '',
        userTypeArr: [],//用户类型
        userTypeText: '',
        loginPassword: '',
        contactInformationDialog: false,//修改用户上级弹窗
        showDialog: false,// 注销用户弹窗
        id: this.$route.query.id,
        refUserName: this.$route.query.refUserName,//上级用户名
        status: '',
        openTradePassword: '',//开启支付密码
        openMobileCode: '',//开启手机验证码
        openGoogleCode: '',//开启Google验证
        userType: '',//用户类型
        synStatus: '',//注册数据同步状态
      }
    },
    created() {
      // this.getType()
    },
    watch: {
      // Keyword: {
      //   handler(newName, oldName) {
      //     if (newName !== oldName) {
      //       this.bountyProductList = []
      //       this.isChange = true
      //     }
      //   },
      //   immediate: true
      // }
    },
    methods: {
      // 修改上级
      modiftyBtn(){
        this.contactInformationDialog = true
      },
      // 确定修改
      modifSure() {
        if(!this.id) {
          Message.error('请填写用户Id')
          return
        }
        if(!this.refUserName) {
          Message.error('请填写上级用户名')
          return
        }
        this.$fetch.post('/user/userAction/updateUserLevel', {
          userId: this.id,
          refUserName: this.refUserName
        }).then(res => {
          if(res.code === 0) {
            this.contactInformationDialog = false
            Dialog({
              title: '',
              content: res.msg,
              okFn: () => {
                this.getData()
              }
            })
          }
        }).catch(err => {
          console.log(err)
        })
      },
      // 注销
      cancelBtn() {
        this.showDialog = true
      },
      // 修改用户上级
      handleClose() {
        this.contactInformationDialog = false
      },
      // 注销
      paySure() {
        this.$fetch.post(`/user/userAction/cancellation`,{
          id: this.id
        }).then(res => {
          if(res.code === 0) {
            this.showDialog = false
            Dialog({
              title: '',
              content: res.msg,
            })
          }
        }).catch(err => {
          console.log(err)
        })
      },
      // 枚举
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=user.User$GenderEnum;user.User$StatusEnum;user.User$TradePwdEnum;user.User$MobileEnum;user.User$UserTypeEnum;user.User$GoogleEnum&flag=false&state=1`).then(res => {
          if (res.code === 0) {
            this.genderArr = res.data.data.GenderEnum
            this.statusArr = res.data.data.StatusEnum
            this.tradeArr = res.data.data.TradePwdEnum
            this.mobileArr = res.data.data.MobileEnum
            this.googleArr = res.data.data.GoogleEnum
            this.userTypeArr = res.data.data.UserTypeEnum
          }
        })
      },
      // 注册数据同步状态
      handleCommand8(command) {
        this.synStatusText = command.text
        this.dataDetail.synStatus = command.id
      },
      // 是否注册openfire用户
      handleCommand7(command) {
        this.openfireText = command.text
        this.dataDetail.openfire = command.id
      },
      // 用户类型
      handleCommand6(command) {
        this.userTypeText = command.text
        this.dataDetail.userType = command.id
      },
      // Gogole验证码
      handleCommand5(command) {
        this.googleText = command.text
        this.dataDetail.openGoogleCode = command.id
      },
      // 手机验证码
      handleCommand4(command) {
        this.mobileText = command.text
        this.dataDetail.openMobileCode = command.id
      },
      // 支付密码
      handleCommand3(command) {
        this.tradeText = command.text
        this.dataDetail.openTradePassword = command.id
      },
      // 性别
      handleCommand(command) {
        this.genderText = command.text
        this.dataDetail.gender = command.id
      },
      //账户状态
      handleCommand2(command) {
        this.statusText = command.text
        this.dataDetail.status = command.id
      },
      verify() {
        if(!this.userName) {
          Message.error('请填写用户名')
          return
        }
        // if(!this.acceptUserName) {
        //   Message.error('请填写接收者用户名')
        //   return
        // }
        // if(!this.amount) {
        //   Message.error('请填写金额')
        //   return
        // }
        // if(!this.currencyText) {
        //   Message.error('请选择币种')
        //   return
        // }
        // if(!this.code) {
        //   Message.error('请填写安全码')
        //   return
        // }
        return true
      },
      // 保存
      sureBtn(){
        if(this.verify()) {
          this.loadShow  = true
          this.$fetch.post('/userNone/userNoneData/queryUserNoneData',{
            userName: this.userName,
          }).then(res => {
            this.loadShow  = false
            if(res.data) {
              this.dataShow = true
              let dataDetail = res.data.page.list[0]
              if (dataDetail) {
                for(let i in dataDetail) {
                  if (dataDetail[i] === null || dataDetail[i] === "") {
                    dataDetail[i] = '-'
                  }
                }
              }
              this.dataDetail = dataDetail
            } else {
              this.inEmpty = true
              this.dataShow = false
            }
          }).catch(err => {
            Message.error(res.msg)
          })
        }
      },
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
  .inEmpty{
    padding 20px
    width 100%
    height 100%
    background-color #fff
  }
    .ul-w{
      width 100%
      height calc(100vh - 200px)
      background-color #fff
    }
  .zoom-enter-to{
    transition: none;
  }
  .el-dropdown-menu{
    width 260px
  }
  .dialog-input{
    width 240px!important
  }
  .problem-box{
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
    padding-bottom 0
    position relative
    display flex
    flex-direction row
    flex-wrap wrap
    .search-box
      margin-bottom 0
      display flex
      flex-direction row
      align-items center
      .input-wrap
        width 280px
        input
          width 100%
        textarea
          width 260px
          height 100px
          padding 10px
          border 1px solid #DCDFE6
          background #fff
      .label
        width 150px
        color #b2b3bc
      .dropdown-wrap
        width 280px!important
        cursor pointer
        text-align center
        line-height 40px
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
  >>>.dropdown-wrap{
    min-width: 160px !important;
  }
  /*>>> .el-dialog .el-dialog__headerbtn .el-icon-close:before {*/
  /*  border: 2px solid #ccc;*/
  /*  border-radius: 100%;*/
  /*}*/
  
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
</style>>
