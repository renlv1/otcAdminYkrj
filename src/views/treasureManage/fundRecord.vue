<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>基金记录</span>
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
          <div class="label">支付状态：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{payText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 1)" v-for="(item, index) in payArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">是否已累计：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{totalText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 2)" v-for="(item, index) in totalArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">类型：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{fundText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 3)" v-for="(item, index) in fundArr" :key="index">{{item.text}}
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
      <list-wrap :list="isListNull(list)" :total="total" @change="getData">
        <div class="web-table">
          <table class="center">
            <tr class="thead">
              <td width="6%">ID</td>
              <td>用户名</td>
              <td>用户地址</td>
              <td>基金金额</td>
              <td>支付状态</td>
              <td>支付ID</td>
              <td>是否已累计</td>
              <td>类型</td>
              <td>支付时间</td>
              <td>创建时间</td>
            </tr>
            <tr v-for="(item, index) in list" :key="index" class="tbody-tr">
              <td>{{item.id}}</td>
              <td>{{item.userName}}</td>
              <td>{{item.userAddress}}</td>
              <td>{{item.fundAmount}}</td>
              <td>{{payStateFn(item.payState)}}</td>
              <td>{{item.payId}}</td>
              <td>{{totalFlagFn(item.totalFlag)}}</td>
              <td>{{fundTypeFn(item.fundType)}}</td>
              <td class="td-content" v-html="timeStrDate(item.payDateAt)"></td>
              <td v-html="timeStrDate(item.createAt)"></td>
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
  import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        payArr: [],
        totalArr: [],
        fundArr: [],
        fundText: '',
        totalText: '',
        valueID: '',
        orderID: '',//订单id
        userName: '', //用户名
        accountAddress: '',//用户地址
        replyNum: '', // 回复次数
        fromArr: [],
        problemFrom: '',
        typeArr: [],
        protype: '',
        statusArr: [],
        statusText: '',
        paymentArr: [
          {text: '给用户打钱',id: 1},
          {text: '给老板打钱',id: 2},
        ],
        errTip: '',
        stateDialog:false,
        payType: '',
        payText: '',
        problemStatus:'',
        status: '',
        list: [],
        total: 0,
        resourceId: '',//问题来源
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        showDialog: false,
        contentType: '',// 问题状态
        payId: '',
        stateID: '',
        tyepID: '',//问题类型
        totalFlag:'',
        fundType: '',
      }
    },
    created() {
      this.getType()
      // this.getData()
    },
    methods: {
      // 枚举
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=fund.OneFundRecord$FundTypeEnum;fund.OneFundRecord$PayStateEnum;fund.OneFundRecord$TotalFlagEnum&flag=true&state=4`).then(res => {
          if (res.code === 0) {
            this.payArr = res.data.data.PayStateEnum
            this.totalArr = res.data.data.TotalFlagEnum
            this.fundArr = res.data.data.FundTypeEnum
          }
        })
      },
      payStateFn(val) {
        return this.payArr[val].text
      },
      totalFlagFn(val) {
        return this.totalArr[val].text
      },
      fundTypeFn(val) {
        return this.fundArr[val].text
      },
      // 重置
      resetFields() {
        this.fundText = ''
        this.fundType = ''
        this.totalText = ''
        this.totalFlag = ''
        this.payText =''
        this.payId = ''
        this.userName = ''
        this.accountAddress = ''
        this.protype = ''
        this.problemStatus = ''
        this.problemFrom = ''
        this.replyNum = ''
        this.resourceId = ''
        this.tyepID = ''
        this.contentType = ''
        this.orderID = ''
        this.valueID = ''
      },
      commandChange (commend) {
        if (commend.index === 1) {
          this.payText = commend.item.text
          this.payId = commend.item.id
        } else if (commend.index === 2) {
          this.totalText = commend.item.text
          this.totalFlag = commend.item.id
        } else if (commend.index === 3) {
          this.fundText = commend.item.text
          this.fundType = commend.item.id
        }
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/fund/oneFund/queryOneFundList', {
          id: this.valueID,
          userName: this.userName, //用户名
          payState : this.payId === 'selected' ? this.payId = '' : this.payId,//支付状态
          totalFlag: this.totalFlag  === 'selected' ? this.totalFlag  = '' : this.totalFlag ,//是否累计
          fundType : this.fundType  === 'selected' ? this.fundType  =  '' : this.fundType ,// 类型
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
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
  .dialog-input{
    width 240px!important
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
    padding 30px 20px!important
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
