<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>基金合计详情</span>
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
          <div class="label">下级用户名：</div>
          <div class="input-wrap"><input v-model.trim="childUserName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">数据来源用户名：</div>
          <div class="input-wrap"><input v-model.trim="sourceUserName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">大小边：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="handleCommand2">
              <span class="el-dropdown-link">
                {{payText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in payArr" :key="index">{{item.text}}
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
              <td width="6%">ID</td>
              <td>用户名</td>
              <td width="12%">用户地址</td>
              <td>下级用户名</td>
              <td width="12%">下级用户地址</td>
              <td width="12%">数据来源用户</td>
              <td>来源记录ID</td>
              <td>金额</td>
              <td>大小边</td>
              <td>奖励基数值</td>
              <td>派别累计原始值</td>
              <td>创建时间</td>
              <td>更新时间</td>
            </tr>
            <tr v-for="(item, index) in isListNull(list)" :key="index" class="tbody-tr">
              <td>{{item.id}}</td>
              <td>{{item.userName}}</td>
              <td>{{item.userAddress}}</td>
              <td>{{item.childUserName}}</td>
              <td>{{item.childUserAddress}}</td>
              <td>{{item.sourceUserName}}</td>
              <td >{{item.sourceId}}</td>
              <td>{{item.fundAmount}}</td>
              <td>{{fundTypeFn(item.bigOrSmall)}}</td>
              <td>{{item.rewardBaseAmount}}</td>
              <td>{{item.oldRefTotal}}</td>
              <td v-html="timeStrDate(item.createAt)" ></td>
              <td v-html="timeStrDate(item.updateAt)"></td>
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
        childUserName: '',
        sourceUserName: '',
        refUserName: '',
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
        this.$fetch.get(`/public/enumValue?classPackage=fund.OneFundTotalDetail$BigOrSmallEnum&flag=true&state=4`).then(res => {
          if (res.code === 0) {
            this.payArr = res.data.data.BigOrSmallEnum
          }
        })
      },
      fundTypeFn(val) {
        return this.payArr[val].text
      },
      // 重置
      resetFields() {
        this.sourceUserName = ''
        this.childUserName = ''
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
      // 奖励发放标识
      handleCommand2(command) {
        this.payText = command.text
        this.payId = command.id
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/fund/fundDetail/queryFundDetailList', {
          id: this.valueID,
          userName: this.userName, //用户名
          childUserName: this.childUserName,
          sourceUserName: this.sourceUserName,
          bigOrSmall : this.payId === 'selected' ? this.payId = '' : this.payId,
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
    },
    
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
