<template>
  <div class="transaction-list">
    <!--转账记录管理-->
    <div class="crumb">
      <span>充值信息</span>
    </div>
    <div class="page-wrap a-el-tab">
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="待接单" name="first"></el-tab-pane>
        <el-tab-pane label="待老板确认收款" name="second"></el-tab-pane>
        <el-tab-pane label="全部状态" name="third"></el-tab-pane>
      </el-tabs>
      <div class="search clearfix">
        <div class="input-item" v-show="tabIndex !== 2">
          <div class="label">充值用户名</div>
          <div class="input-wrap"><input type="text" v-model.trim="waitName"></div>
        </div>
        <div v-show="tabIndex === 2">
          <div class="input-item" >
            <div class="label">ID</div>
            <div class="input-wrap"><input type="number" v-model.trim="userId"></div>
          </div>
          <div class="input-item" >
            <div class="label">用户名</div>
            <div class="input-wrap"><input type="text" v-model.trim="userName"></div>
          </div>
          <div class="input-item" >
            <div class="label">老板名</div>
            <div class="input-wrap"><input type="text" v-model.trim="bossName"></div>
          </div>
          <div class="input-item">
            <div class="label">支付状态</div>
            <div class="input-wrap dropdown-input">
              <el-dropdown trigger="click" @command="commandChange">
                <span class="el-dropdown-link">
                  {{orderTypeText}}<i class="el-icon-arrow-down el-icon--right"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu" slot="dropdown">
                  <el-dropdown-item :command="composeValue(item,1)" v-for="(item, index) in payStatusArr" :key="index">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </div>
          <div class="input-item">
            <div class="label">系统标识</div>
            <div class="input-wrap dropdown-input">
              <el-dropdown trigger="click" @command="commandChange">
                <span class="el-dropdown-link">
                  {{stateEnumText}}<i class="el-icon-arrow-down el-icon--right"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu" slot="dropdown">
                  <el-dropdown-item :command="composeValue(item, 2)" v-for="(item, index) in sysBossFlagArr" :key="index">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </div>
          <div class="input-item">
            <div class="label">状态</div>
            <div class="input-wrap dropdown-input">
              <el-dropdown trigger="click" @command="commandChange">
                <span class="el-dropdown-link">
                  {{callbackStatus}}<i class="el-icon-arrow-down el-icon--right"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu" slot="dropdown">
                  <el-dropdown-item :command="composeValue(item, 3)" v-for="(item, index) in orderStatusArr" :key="index">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </div>
          <div class="input-item">
            <div class="label">时间</div>
            <div class="input-wrap">
              <el-date-picker
                      v-model="startDate"
                      placeholder="开始时间"
                      :picker-options="pickerBeginDateBefore"
                      format="yyyy-MM-dd HH:mm:ss"
                      default-time="00:00:00"
                      type="datetime">
              </el-date-picker>
            </div>
            <span class="margin">到</span>
            <div class="input-wrap">
              <el-date-picker
                      :picker-options="pickerBeginDateAfter"
                      placeholder="结束时间"
                      format="yyyy-MM-dd HH:mm:ss"
                      default-time="23:59:59"
                      v-model="endDate"
                      type="datetime">
              </el-date-picker>
            </div>
          </div>
        </div>
        <div class="input-item btn-ctrl-w">
          <div class="btn-ctrl btn-empty" @click="emptyFn">清空</div>
          <div class="btn-ctrl" @click="search">查询</div>
        </div>
      </div>
      <div class="flag-t" v-show="isShow">待用户打款:{{flagObj.waitMoney}}，确认收款:{{flagObj.getAmount}}，已完成:{{flagObj.getFinAmount}}，仲裁中:{{flagObj.getZhongcaiDoingAmount}}，仲裁完成:{{flagObj.getZhongcaiAmount}}</div>
      <list-wrap :list="listData" :total="totalData" @change="getData" :pageSize="pageSize">
        <div class="web-table">
          <table>
            <tr class="thead">
              <td>ID</td>
              <td>用户名</td>
              <td>老板名</td>
              <td>充值金额</td>
              <td>币种</td>
              <td>打款账号</td>
              <td>手续费</td>
              <td>收款金额</td>
              <td>订单状态</td>
              <td>支付状态</td>
              <td>创建时间</td>
              <td>接单时间</td>
              <td>操作</td>
            </tr>
            <tr v-for="(item, index) in isListNull(listData)" :key="index">
              <td>{{item.id}}</td>
              <td>{{item.username}}</td>
              <td>{{item.bossName}}</td>
              <td>{{item.depositAmount}}</td>
              <td>{{item.depositCurrency}}</td>
              <td>{{item.giveAccount}}</td>
              <td>{{item.feeAmount}}</td>
              <td>{{item.receiveAmount}}</td>
              <td>{{orderStatusText(item.status)}}</td>
              <td>{{payStatusText(item.payStatus)}}</td>
              <td v-html="timeStrDate(item.createAt)" class="nowrap"></td>
              <td v-html="timeStrDate(isEmptyText(item.acceptAt))" class="nowrap"></td>
              <td class="btn-ctrl">
                <router-link tag="a" :to="{ path: '/trade/deposit/depositDetail',query: {id:item.id}}">查看</router-link>
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

<script type="text/ecmascript-6">
/*eslint-disable*/
import Dialog from '@/components/dialog/dialog'

export default {
  data() {
    return {
      loadShow:false,
	    sysBossFlagArr: [
		    {id: 'selected', text: '全部'},
		    {id: 0, text: '不是'},
		    {id: 1, text: '是'},
      ],
	    bossName: '',
	    waitName: '',
	    userId: '',
    	tabIndex: 0,
	    activeName: 'first',
	    payStatusArr: [],
	    orderStatusArr: [],
	    userName: '',
      searchId: '',
      dialogFormVisible: false,
      pageSize: 20,
      totalData: 0,
      dialogVisible: false,
      listData: [],
      transferId: '',
      transferOutUser: '',
      transferOutAddress: '',
      receiveUser: '',
      receiveAddress: '',
      businessOrder: '',
      startDate: '',
      endDate: '',
      callbackState: '',
      state: '',
      orderTypeState: '',
      total: 0,
      searchVal: '',
      //        日期控件
      pickerBeginDateBefore: {
        disabledDate: (time) => {
          let beginDateVal = this.endDate
          if (beginDateVal) {
            return time.getTime() > beginDateVal
          }
        }
      },
      pickerBeginDateAfter: {
        disabledDate: (time) => {
          let beginDateVal = this.startDate
          if (beginDateVal) {
            return time.getTime() < beginDateVal
          }
        }
      },
      pageIndex: 1,
      callbackStatus: '全部',
      stateEnumText: '全部',
      orderTypeText: '全部',
      callbackEnum: [],
      stateEnum: [],
      orderType: [],
      flagObj: {},
	    isShow: false,
    }
  },
  created() {
    this.getStatus()
  },
  methods: {
	  handleClick(tab, event) {
	  	this.tabIndex = Number(tab.index)
      this.emptyFn()
	  },
	  orderStatusText (type) {
	  	return this.orderStatusArr[type + 1].text
    },
	  payStatusText  (type) {
		  return this.payStatusArr[type].text
	  },

    emptyFn() {
      this.userId = ''
      this.userName = ''
      this.startDate = ''
      this.bossName = ''
      this.endDate = ''
	    this.waitName = ''
      this.callbackState = ''
      this.state = ''
      this.orderTypeState = ''
      this.callbackStatus = '全部'
      this.stateEnumText = '全部'
      this.orderTypeText = '全部'
    },

    btnCtrlFn(id) {
      this.$router.push({
        path: '/trade/deposit/depositDetail',
        query: {id}
      })
    },
    // 获取状态
    getStatus() {
      this.$fetch.get(`/public/enumValue?classPackage=finance.DepositRecord$DepositStatusEnum;finance.DepositRecord$DepositPayStatusEnum&flag=true&state=1`).then(res => {
        if (res.code === 0) {
        	let arr1 = res.data.data.DepositPayStatusEnum
	        arr1.forEach((item, index) => {
	        	if(index === 1) {
	        		item.text = '待接单'
            }
          })
	        this.orderStatusArr = res.data.data.DepositStatusEnum
            this.payStatusArr = arr1
        }
      })
    },
    // 获取列表数据
    getData(index, pageSize) {
	    this.isShow = false
      if (parseInt(this.total) !== 0) {
        if (pageSize) {
          this.pageSize = pageSize
        }
        let dataObj = {
          pageIndex: index,
          pageSize: this.pageSize
        }
        if (this.tabIndex === 0) {
	        dataObj.remark = 1
        	if (this.waitName) {
        		dataObj.username = this.waitName
	        }
        } else if (this.tabIndex === 1) {
	        dataObj.remark = 4
	        if (this.waitName) {
	        	dataObj.username = this.waitName
	        }
        } else if (this.tabIndex === 2) {
	        if (this.userId) dataObj.id = this.userId
	        if (this.userName) dataObj.username = this.userName
	        if (this.bossName) dataObj.bossName = this.bossName
	        if (this.state > -1) dataObj.sysBossFlag  = this.state
	        if (this.callbackState > -1) dataObj.status = this.callbackState
	        if (this.orderTypeState > -1) dataObj.payStatus = this.orderTypeState
	        if (this.startDate) dataObj.startAtStr = this.$changeDate(this.startDate, '-', 8)
	        if (this.endDate) dataObj.endAtStr  = this.$changeDate(this.endDate, '-', 8)
        }
        this.loadShow = true
        this.$fetch.post(`/finance/deposit/queryDepositList`, dataObj).then(res => {
        	this.flagObj = res.data
          if (this.tabIndex === 2) {
          	this.isShow = true
          }
          this.loadShow = false
          if(res.data.page) {
            this.listData = res.data.page.list
            this.totalData = res.data.page.totalCount
          } else {
            this.listData = []
            this.totalData = 0
          }
        })
      }
    },
    commandChange (commend) {
	    if (commend.index === 1) {
          this.orderTypeText = commend.item.text
          this.orderTypeState = commend.item.id
        } else  if (commend.index === 2) {
          this.stateEnumText = commend.item.text
          this.state = commend.item.id
        } else  if (commend.index === 3) {
          this.callbackStatus = commend.item.text
          this.callbackState = commend.item.id
        }
    },
    //
    dropFN1(obj) {
      this.orderTypeText = obj.text
      this.orderTypeState = Number(obj.id)
    },
    //
    dropFN2(obj) {
      this.stateEnumText = obj.text
      this.state = Number(obj.id)
    },
    //
    dropFN3(obj) {
      this.callbackStatus = obj.text
      this.callbackState = Number(obj.id)
    },
    search() {
      this.total = 1 // hook listWrap组件的loading
      this.getData(1, 20)
    },
  }
}
</script>

<style lang="less" scoped>
  .flag-t{
    text-align: right;
    margin-top: 10px;
    color: #409EFF;
  }
</style>


