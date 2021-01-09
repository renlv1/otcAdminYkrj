<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>用户账户复制</span>
    </div>
    <div class="page-wrap">
      <div class="search">
        <div class="search-box">
          <div class="label">ID：</div>
          <div class="input-wrap"><input v-model="id" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">用户名：</div>
          <div class="input-wrap"><input v-model.trim="userName" type="text"></div>
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
                {{ currencyText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in currencyArr" :key="index">{{item.text}}
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
          <div class="center">
            <div class="common-thead">
              <div class="thead" width="6%">ID</div>
              <div class="thead">用户名</div>
              <div class="thead tbody-address">用户地址</div>
              <div class="thead">余额</div>
              <div class="thead">冻结余额</div>
              <div class="thead">委托冻结金额</div>
              <div class="thead">币种</div>
              <div class="thead">创建时间</div>
              <div class="thead">更新时间</div>
              <div class="thead">操作</div>
            </div>
            <div v-for="(item, index) in isListNull(list)" :key="index" class="tbody-wrap">
              <div class=" common-tbody">
                <div class="tbody">{{item.id}}</div>
                <div class="tbody">{{item.userName}}</div>
                <div class="tbody tbody-address" >{{item.userAddress}}</div>
                <div class="tbody">{{item.balance}}</div>
                <div class="tbody">{{item.balanceFreeze}}</div>
                <div class="tbody">{{item.balanceEntrustFreeze}}</div>
                <div class="tbody">{{item.currency}}</div>
                <div class="tbody" v-html="timeStrDate(item.createTime)"></div>
                <div class="tbody" v-html="timeStrDate(item.updateTime)">{{$changeDate(item.updateTime)}}</div>
                <div @click="upload(item)" class="blue-td cursor tbody" v-show="item.packFlag === false">
                  <span >展开</span>
<!--                  <span v-show="item.packFlag === true">收起</span>-->
                  <img src="../../../src/assets/img/kai.png">
<!--                  <i class="icon" :class="{active: item.packFlag === true}"></i>-->
                </div>
                <div @click="shouqi(item)" class="blue-td cursor tbody" v-show="item.packFlag === true">
                  <span>收起</span><img src="../../../src/assets/img/shou.png">
                </div>
              </div>
              <!--              v-show="item.packFlag === true"-->
              <div class="add-more" v-show="item.packFlag === true">
                <div class="add-up">
                  <span class="">备注：{{item.remarks || '-'}}</span>
                  <span>备注字段1：{{item.backup1 || '-'}}</span>
                  <span>备注字段2：{{item.backup2 || '-'}}</span>
                  <span>备注字段3：{{item.backup3 || '-'}}</span>
                  <span>第三方转入余额：{{item.transfertoBalance || 0}}</span>
                  <span>锁定的余额：{{item.balanceLock}}</span>
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
  // import { Message } from 'element-ui'
  // import Dialog from '@/components/dialog/dialog'
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
        currencyText: '',
        currencyArr: [],
        id: '',
        userName: '',//用户名
        startTime: '',
        endTime: '',
        list: [],
        total: 0,
        pageIndex: 1,
        loadShow: false,
        userAddress: '',//用户地址
        currency: '',//币种
        pageSize: 10,
      }
    },
    created() {
      this.getType()
    },
    methods: {
      upload(item) {
        item.packFlag = !item.packFlag
      },
      shouqi(item) {
        item.packFlag = !item.packFlag
      },
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=user.UserAccount$CurrencyTypeEnum;user.UserAccount$OperationEnum;&flag=true&state=1`).then(res => {
          if (res.code === 0) {
            this.currencyArr = res.data.data.CurrencyTypeEnum
          }
        })
      },
      resetFields() {
        this.currencyText = ''
        this.currency = ''
        this.id = ''
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
        this.$fetch.post('/user/accountCopy/queryUserAccountCopyList', {
          id: this.id,
          userAddress: this.userAddress,//用户地址
          currency:this.currency === 'selected' ? this.currency = '' : this.currency,//币种
          userName: this.userName, //用户名
          startAtStr : this.$changeDate(this.startTime, '-', 8), //创建-更新时间
          endAtStr: this.$changeDate(this.endTime, '-', 8), //最后登录时间
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize
        }).then((res) => {
          this.loadShow = false
          if( res.data) {
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
  .tbody{
    padding 0
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
