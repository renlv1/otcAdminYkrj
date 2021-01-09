<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb detail-page">
      <div class="title-left">
        <router-link to="/trade/upServerBoss" class="title-m">服务共享者 </router-link>
        <span> / 详情</span>
      </div>
      <div class="btn-right">
        <span class="btn-save" @click="saveBtn()">保存</span>
      </div>
    </div>
    <div class="page-wrap detail-cont">
      <ul class="ul-w ">
        <li class="li-item search">
          <div class="search-box">
            <div class="label">ID</div>
            <div class="input-wrap">{{dataDetail.id}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">用户名</div>
            <div class="input-wrap"><input v-model="dataDetail.userName"></div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">用户地址</div>
            <div class="input-wrap">{{dataDetail.userAddress}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">共享者名称</div>
            <div class="input-wrap"><input v-model="dataDetail.bossName"></div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">状态</div>
            <div class="input-wrap dropdown-wrap">
              <el-dropdown @command="handleCommand">
              <span class="el-dropdown-link">
                {{ genderText}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
                <el-dropdown-menu class="menu" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in genderArr" :key="index">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">共享者地址</div>
            <div class="input-wrap">{{dataDetail.bossAddress || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">创建时间</div>
            <div class="input-wrap">{{dataDetail.createAt || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">更新时间</div>
            <div class="input-wrap">{{dataDetail.updateAt || '-'}}</div>
          </div>
        </li>
      </ul>
    </div>
    <el-dialog width="30%" :visible.sync="orderDialog">
      <div class="problem-box">
        <img src="../../../assets/img/warning.png"><label>确认保存？</label>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="orderDialog = false">取 消</el-button>
        <el-button type="primary" @click="paySure()">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  import Dialog from '@/components/dialog/dialog'
  import { Message } from 'element-ui'
  export default {
    name: "transferLogDetail",
    data() {
      return{
        orderDialog:false,
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
      this.getType()
      this.getData()
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
      // 枚举
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=finance.UpServeBoss$StatusEnum&flag=false&state=1`).then(res => {
          if (res.code === 0) {
            this.genderArr = res.data.data.StatusEnum
          }
        })
      },
      handleCommand(command) {
        this.genderText = command.text
        this.gender = command.id
      },
      verify() {
        // if(!this.sendUserName) {
        //   Message.error('请填写发送者用户名')
        //   return
        // }
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
      paySure() {
        if(this.verify()) {
          this.$fetch.post('/finance/upServerBoss/updateUpServerBossInfo',{
            id: this.id,
            userName: this.dataDetail.userName,
            bossName: this.dataDetail.bossName,
            status: this.gender
          }).then(res => {
            if(res.code === 0) {
              this.orderDialog = false
              Dialog({
                title: '',
                content: res.msg,
                okFn: () => {
                  this.getData()
                }
              })
            }
          })
        }
      },
      // 保存
      saveBtn(){
        this.orderDialog = true
      },
      getData(){
        this.$fetch.post(`/finance/upServerBoss/getUpServerBossInfo`,{
          id: this.id
        }).then(res => {
          this.dataDetail = res.data.resultData
          this.genderFn( this.dataDetail.status)
        })
      },
      // 显示性别
      genderFn(val) {
        if(Number(val) === 1)  return this.genderText = '正常'
        if (Number(val) === 2) return  this.genderText = '删除'
        return  this.genderText = '未选择'
      },
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
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
