<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>加速分析记录</span>
    </div>
    <div class="page-wrap">
      <div class="search">
        <div class="search-box">
          <div class="label">ID：</div>
          <div class="input-wrap"><input v-model="id" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">被加速用户名：</div>
          <div class="input-wrap"><input v-model.trim="sendUserName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">下级用户名：</div>
          <div class="input-wrap"><input v-model.trim="receiveUserName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">加速来源ID：</div>
          <div class="input-wrap"><input v-model="realSourceAssignId" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">大小边：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{BigOrSmallText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 1)" v-for="(item, index) in BigOrSmallArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">加速标识：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{ payText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu payment" slot="dropdown" >
                <el-dropdown-item :command="composeValue(item, 2)" v-for="(item, index) in payArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">加速类型：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{orderText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 3)" v-for="(item, index) in orderArr" :key="index">{{item.text}}
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
            :picker-options="pickerBeginDateBefore"
            format="yyyy-MM-dd HH:mm:ss"
          >
          </el-date-picker>
          
          <span style="margin-right: 10px">到</span>
          
          <el-date-picker
            placeholder="结束时间"
            v-model="endTime"
            type="datetime"
            default-time="23:59:59"
            :picker-options="pickerBeginDateAfter"
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
      <list-wrap :list="isListNull(list)" :total="total" @change="getData">
        <div class="web-table">
          <table class="center">
            <tr class="thead">
              <td width="6%">ID</td>
              <td>用户名</td>
              <td>下级用户名</td>
              <td>加速来源记录ID</td>
              <td>加速值</td>
              <td>实际加速值</td>
              <td>加速标识</td>
              <td>加速类型</td>
              <td>大小边</td>
              <td>派别原累计值</td>
              <td>创建时间</td>
              <td>统计标识</td>
            </tr>
            <tr v-for="(item, index) in list" :key="index" class="tbody-tr">
              <td>{{item.id}}</td>
              <td>{{item.userName}}</td>
              <td>{{item.childUserName}}</td>
              <td>{{item.realSourceAssignId}}</td>
              <td>{{item.expediteAmount}}</td>
              <td>{{item.realExpediteAmount}}</td>
              <td>{{expediteFlagFn(item.expediteFlag)}}</td>
              <td>{{expediteTypeFn(item.expediteType)}}</td>
              <td>{{bigOrSmallFn(item.bigOrSmall)}}</td>
              <td>{{item.oldRefTotal}}</td>
              <td v-html="timeStrDate(item.createAt)"></td>
              <td>{{totalFlagFn(item.totalFlag)}}</td>
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
        pickerBeginDateBefore: {
          disabledDate: (time) => {
            let beginDateVal = this.endTime
            if (beginDateVal) {
              return time.getTime() > beginDateVal
            }
          }
        },
        pickerBeginDateAfter: {
          disabledDate: (time) => {
            let beginDateVal = this.startTime
            if (beginDateVal) {
              return time.getTime() < beginDateVal
            }
          }
        },
        realSourceAssignId: '',
        BigOrSmallArr: '',
        BigOrSmallText: '',
        id: '',
        sendUserName: '',//发送用户
        receiveUserName: '',//接受用户
        stateText: '',
        stateArr: [],
        payArr: [
          {id:'selected',text: '全部'},
          {id:1,text: '直上级直接加速'},
          {id:2,text: '上级间接加速'},
          {id:3,text: '直上级间接加速'}
        ],
        payText: '',
        orderArr: [
          {id:'selected',text: '全部'},
          {id:1,text: '正常藏宝'}
        ],
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
        totalFlagArr: [],
      }
    },
    created() {
      this.getType()
    },
    methods: {
      bigOrSmallFn(index){
        return this.BigOrSmallArr[index+1].text
      },
      expediteFlagFn(index){
        return this.payArr[index].text
      },
      expediteTypeFn(index) {
        return this.orderArr[index].text
      },
      totalFlagFn(index){
        return this.totalFlagArr[index].text
      },
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=map.ExpediteAnalyzeRecord$BigOrSmallEnum;map.ExpediteAnalyzeRecord$TotalFlagEnum&flag=true&state=4`).then(res => {
          if (res.code === 0) {
            this.BigOrSmallArr = res.data.data.BigOrSmallEnum
            this.totalFlagArr = res.data.data.TotalFlagEnum
          }
        })
      },
      resetFields() {
        this.expediteFlag = ''
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
        this.bigOrSmall = ''
        this.realSourceAssignId = ''
        this.BigOrSmallText = ''
      },
      commandChange (commend) {
        if (commend.index === 1) {
          this.BigOrSmallText = commend.item.text
          this.bigOrSmall = commend.item.id
        } else if (commend.index === 2) {
          this.payText = commend.item.text
          this.paymentType = commend.item.id
        } else if (commend.index === 3) {
          this.orderText = commend.item.text
          this.orderType = commend.item.id
        }
      }, 
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/map/expediteAnalyze/queryExpediteAnalyzeList', {
          id: this.id,
          userName: this.sendUserName,// 发送用户
          childUserName: this.receiveUserName,//
          realSourceAssignId: this.realSourceAssignId,
          bigOrSmall: this.bigOrSmall === 'selected' ? this.bigOrSmall = '' : this.bigOrSmall, //状态
          expediteFlag: this.paymentType === 'selected' ? this.paymentType = '' : this.paymentType,//加速标识
          expediteType: this.orderType === 'selected' ? this.orderType = '' : this.orderType,//加速类型
          startCreateAtStr : this.$changeDate(this.startTime, '-', 8), //开始-更新时间
          endCreateAtStr: this.$changeDate(this.endTime, '-', 8), //最后登录时间
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
  .el-dropdown-menu{
    max-height 300px!important
    overflow auto !important
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
