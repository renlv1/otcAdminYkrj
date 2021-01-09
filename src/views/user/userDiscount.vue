<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>用户折扣</span>
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
          <div class="label">用户地址：</div>
          <div class="input-wrap"><input v-model.trim="accountAddress" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">状态：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{statusText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 1)" v-for="(item, index) in statusArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">类别：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{userText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 2)" v-for="(item, index) in userArr" :key="index">{{item.text}}
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
              <td width="15%">用户地址</td>
              <td>类别</td>
              <td width="15%">可以使用的优惠总值</td>
              <td>状态</td>
              <td>创建时间</td>
              <td>更新时间</td>
            </tr>
            <tr v-for="(item, index) in isListNull(list)" :key="index" class="tbody-tr">
              <td>{{item.id}}</td>
              <td>{{item.userName}}</td>
              <td>{{item.userAddress}}</td>
              <td>{{$categoryType(item.category)}}</td>
              <td >{{item.amount}}</td>
              <td>{{$discountStatus(item.status)}}</td>
              <td>{{$changeDate(item.createTime)}}</td>
              <td>{{$changeDate(item.updateTime)}}</td>
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
  // import { Message } from 'element-ui'
  // import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        userName: '',//用户名
        valueID: '',
        accountAddress: '',//用户地址
        statusText: '',
        statusArr: [],//状态
        userText: '',
        userArr: [],//类别
        startTime: '',
        endTime: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        status: '',//状态
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
        userType: '',//类别
      }
    },
    created() {
      this.getType()
      // this.getData()
    },
    methods: {
      // 枚举
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=user.UserDiscount$StatusEnum;user.UserDiscount$CategoryEnum&flag=true&state=1`).then(res => {
          if (res.code === 0) {
            this.statusArr = res.data.data.StatusEnum
            this.userArr = res.data.data.CategoryEnum
          }
        })
      },
      // 重置
      resetFields() {
        this.userText = ''
        this.statusText= ''
        this.userName = ''
        this.accountAddress = ''
        this.startTime = ''
        this.userType = ''
        this.endTime = ''
        this.status = ''
        this.valueID = ''
      },
      commandChange (commend) {
        let command = commend.item
        let id = commend.index
        if (id === 1) {
          this.statusText = command.text
          this.status = command.id
        } else if (id === 2) {
          this.userText = command.text
          this.userType = command.id
        }
      },
      // 账户状态
      handleCommand2(command) {
        this.statusText = command.text
        this.status = command.id
      },
      //用户类型
      handleCommand3(command) {
        this.userText = command.text
        this.userType = command.id
      },
      getData(pageIndex,pageSize) {
        this.loadShow = true
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.$fetch.post('/user/userDiscount/queryUserDiscountList', {
          id: this.valueID,
          userName: this.userName, //用户名
          userAddress: this.accountAddress, //用户地址
          category: this.userType === 'selected' ? this.userType = '' : this.userType, // 类别
          status: this.status === 'selected' ? this.status = '' : this.status, //状态
          startAtStr: this.$changeDate(this.startTime, '-', 8), //创建-结束时间
          endAtStr:  this.$changeDate(this.endTime, '-', 8),
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
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
  .crumb
    display flex
    justify-content space-between
  .dialog-input{
    width 240px!important
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
</style>
