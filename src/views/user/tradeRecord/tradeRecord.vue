<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>交易记录</span>
      <router-link to="/user/tradeRecord/insideTransfer" class="pay-money">内部转账 </router-link>
    </div>
    <div class="page-wrap">
      <div class="search">
        <div class="search-box">
          <div class="label">ID：</div>
          <div class="input-wrap"><input v-model="id" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">发送用户：</div>
          <div class="input-wrap"><input v-model.trim="sendUserName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">接收用户：</div>
          <div class="input-wrap"><input v-model.trim="receiveUserName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">状态：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{ stateText|| '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 1)" v-for="(item, index) in stateArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">支付类型：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{ payText|| '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu payment" slot="dropdown" >
                <el-dropdown-item :command="composeValue(item, 2)" v-for="(item, index) in payArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">订单类型：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{ orderText|| '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item,3)" v-for="(item, index) in orderArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">回调状态：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{callbackText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 4)" v-for="(item, index) in callbackArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">币种：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{currencyText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 5)" v-for="(item, index) in currencyArr" :key="index">{{item.text}}
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
              <td>ID</td>
              <td>订单id</td>
              <td>发送用户</td>
              <td>接收用户</td>
              <td>币种</td>
              <td>金额</td>
              <td>手续费</td>
              <td>状态</td>
              <td>支付类型</td>
              <td>订单类型</td>
              <td>创建时间</td>
              <td>操作</td>
            </tr>
            <tr v-for="(item, index) in list" :key="index" class="tbody-tr">
              <td>{{item.id}}</td>
              <td>{{item.dingdanid}}</td>
              <td>{{item.sendUserName || '-'}}</td>
              <td>{{item.receiveUserName || '-'}}</td>
              <td >{{item.currency}}</td>
              <td>{{item.amount || '-'}}</td>
              <td>{{item.fee || '-'}}</td>
              <td>{{$stateType(item.state)}}</td>
              <td>{{$payType(item.paymentType)}}</td>
              <td>{{orderCate(item.orderType)}}</td>
              <td  v-html="timeStrDate(item.createAt)"></td>
              <td class="blue-td" >
                <router-link tag="a" :to="{path: '/user/tradeRecord/tradeDetail',query: {id: item.id, obj: JSON.stringify(item)}}">查看</router-link>
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
        id: '',
        sendUserName: '',//发送用户
        receiveUserName: '',//接受用户
        stateText: '',
        stateArr: [],
        payArr: [],
        payText: '',
        orderArr: [],
        orderText: '',
        callbackArr: [],
        callbackText: '',
        currencyText: '',
        currencyArr: [],
        startTime: '',
        endTime: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        state: '',//状态
        paymentType: '',//支付类型
        orderType:'',//订单类型
        callbackState: '',//回调状态
        currency: '',//币种
      }
    },
    created() {
      this.getType()
      // this.getData()
    },
    methods: {
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=user.Transaction$CallbackEnum;user.Transaction$StateEnum;user.Transaction$PaymentTypeEnum;user.Transaction$OrderType;user.Transaction$CurrencyTypeEnum;&flag=true&state=1`).then(res => {
          if (res.code === 0) {
            this.stateArr = res.data.data.StateEnum
            this.payArr = res.data.data.PaymentTypeEnum
            this.orderArr = res.data.data.OrderType
            this.callbackArr = res.data.data.CallbackEnum
            this.currencyArr = res.data.data.CurrencyTypeEnum
          }
        })
      },
      resetFields() {
        this.stateText = ''
        this.state = ''
        this.orderText= ''
        this.orderType = ''
        this.payText = ''
        this.paymentType = ''
        this.callbackText = ''
        this.callbackState = ''
        this.currencyText = ''
        this.currency = ''
        this.receiveUserName = ''
        this.sendUserName = ''
        this.id = ''
        this.startTime = ''
        this.endTime = ''
      },
      commandChange (commend) {
        let command = commend.item
        let id = commend.index
        if(id === 1) {
          this.stateText = command.text
          this.state = command.id
        } else if (id === 2) {
          this.payText = command.text
          this.paymentType = command.id
        } else if (id === 3) {
          this.orderText = command.text
          this.orderType = command.id
        } else if (id === 4) {
          this.callbackText = command.text
          this.callbackState = command.id
        } else if (id === 5) {
          this.currencyText = command.text
          this.currency = command.id
        }
      },
      // 币种
      handleCommand5(command) {
        this.currencyText = command.text
        this.currency = command.id
      },
      // 回调状态
      handleCommand4(command) {
        this.callbackText = command.text
        this.callbackState = command.id
      },
      // 支付类型
      handleCommand2(command) {
        this.payText = command.text
        this.paymentType = command.id
      },
      // 订单类型
      handleCommand3(command) {
        this.orderText = command.text
        this.orderType = command.id
      },
      // 状态
      handleCommand(command) {
        this.stateText = command.text
        this.state = command.id
      },
      payType(index) {
        this.payArr.forEach(item => {
          if(Number(item.id) === Number(index) )  return item.text
        })
      },
      orderCate(index) {
        return this.orderArr[index].text
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/user/transaction/queryTransactionList', {
          id: this.id,
          sendUserName: this.sendUserName,// 发送用户
          receiveUserName: this.receiveUserName,// 接受用户
          state: this.state === 'selected' ? this.state = '' : this.state, //状态
          paymentType: this.paymentType === 'selected' ? this.paymentType = '' : this.paymentType,//支付类型
          orderType: this.orderType === 'selected' ? this.orderType = '' : this.orderType,//订单类型
          callbackState: this.callbackState === 'selected' ? this.callbackState = '' : this.callbackState,//回调状态
          currency: this.currency === 'selected' ? this.currency = '' : this.currency,//币种
          createAtStr : this.$changeDate(this.startTime, '-', 8), //开始-更新时间
          updateAtStr:  this.$changeDate(this.endTime, '-', 8), //最后登录时间
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
