<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>加速每日统计</span>
    </div>
    <div class="page-wrap">
      <div class="search">
        <div class="search-box">
          <div class="label">用户名：</div>
          <div class="input-wrap"><input v-model.trim="userName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">下级用户名：</div>
          <div class="input-wrap"><input v-model.trim="accountAddress" type="text"></div>
        </div>
        
        <div class="search-box">
          <div class="label">业绩日期：</div>
          <el-date-picker
            placeholder="选择时间"
            prefix-icon="el-icon-date"
            clear-icon="none"
            v-model="startTime"
            type="datetime"
            :picker-options="pickerOptionsStart"
            value-format="yyyy-MM-dd HH:mm:ss"
            default-time="00:00:00">
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
              <td>下级用户名</td>
              <td width="15%">下级用户地址</td>
              <td>被加速天数</td>
              <td>业绩累计值</td>
              <td>加速总值</td>
              <td>业绩日期</td>
            </tr>
            <tr v-for="(item, index) in list" :key="index" class="tbody-tr">
              <td>{{item.id}}</td>
              <td>{{item.userName}}</td>
              <td>{{item.userAddress}}</td>
              <td>{{item.childUserName}}</td>
              <td>{{item.childUserAddress}}</td>
              <td>{{item.expediteDay}}</td>
              <td>{{item.nextAssignTotal}}</td>
              <td>{{item.expediteAmount}}</td>
              <td v-html="timeStrDate(item.totalDate)"></td>
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
  export default {
    data() {
      return {
        userName: '',//登录账户名
        accountAddress: '',//登录位置IP
        startTime: '',
        endTime: '',
        pickerOptionsStart: {
          disabledDate: (time) => {
            let beginDateVal = this.startTime
            if (beginDateVal) {
              return time.getTime() > beginDateVal
            }
          }
        },
        isArr:[
          {text: '全部',id: ''},
          {text: '是',id: 1},
          {text: '否',id: 2},
        ], //首次登录此设备
        isText: '',
        status: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        statusArr: [],//设备类型
        deviceFlag: '', //是否第一次登陆此设备
        clientType: '',//  设备类型
        equipment: '',
      }
    },
    created() {
      // this.getData()
    },
    methods: {
      resetFields() {
        this.userName = ''
        this.accountAddress = ''
        this.clientType = ''
        this.startTime = ''
        this.endTime = ''
        this.isText = ''
        this.equipment = ''
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/map/expeditetotal/queryExpediteTotalList', {
          userName: this.userName, //
          startTotalDateStr : this.$changeDate(this.startTime, '-', 8), //
          childUserName: this.accountAddress,
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize
        }).then((res) => {
          this.loadShow = false
          if(res.data) {
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
