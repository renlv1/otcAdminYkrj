<template>
  <div class="transaction-list">
    <div class="crumb bread-top">
      <span>老板币种支持</span>
      <div class="add-btn" @click="addBossFn">+ 新增</div>
    </div>
    <div class="page-wrap">
      <div class="search clearfix">
        <div class="input-item">
          <div class="label">ID</div>
          <div class="input-wrap"><input type="number" v-model="transferId"></div>
        </div>
        <div class="input-item">
          <div class="label">老板ID</div>
          <div class="input-wrap"><input type="number" v-model="bossId"></div>
        </div>
        <div class="input-item">
          <div class="label">充值</div>
          <div class="input-wrap dropdown-input">
            <el-dropdown @command="commandChange">
            <span class="el-dropdown-link">
              {{orderTypeText}}<i class="el-icon-arrow-down el-icon--right"></i>
            </span>
              <el-dropdown-menu class="dropdown-menu dropdown-menu-hidden" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 1)" v-for="(item, index) in orderType" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="input-item">
          <div class="label">提现</div>
          <div class="input-wrap dropdown-input">
            <el-dropdown @command="commandChange">
            <span class="el-dropdown-link">
              {{stateEnumText}}<i class="el-icon-arrow-down el-icon--right"></i>
            </span>
              <el-dropdown-menu class="dropdown-menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 2)" v-for="(item, index) in orderType" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="input-item">
          <div class="label">系统标识</div>
          <div class="input-wrap dropdown-input">
            <el-dropdown @command="commandChange">
            <span class="el-dropdown-link">
              {{callbackStatus}}<i class="el-icon-arrow-down el-icon--right"></i>
            </span>
              <el-dropdown-menu class="dropdown-menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 3)" v-for="(item, index) in sysBossFlagEnum" :key="index">{{item.text}}
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
                    format="yyyy-MM-dd HH:mm:ss"
                    default-time="23:59:59"
                    placeholder="结束时间"
                    v-model="endDate"
                    type="datetime">
            </el-date-picker>
          </div>
        </div>
        <div class="input-item btn-ctrl-w">
          <div class="btn-ctrl btn-empty" @click="emptyFn">清空</div>
          <div class="btn-ctrl" @click="search">查询</div>
        </div>
      </div>
      <list-wrap :list="listData" :total="totalData" @change="getData" :pageSize="pageSize">
        <div class="web-table">
          <table>
            <tr class="thead">
              <td >ID</td>
              <td>用户名</td>
              <td>老板ID</td>
              <td>币种</td>
              <td>用户地址</td>
              <td>充值</td>
              <td>提现</td>
              <td>创建时间</td>
              <td>更新时间</td>
              <td>充值费率</td>
              <td>提现费率</td>
              <td>操作</td>
            </tr>
            <tr  v-for="(item, index) in isListNull(listData)" :key="index">
              <td>{{item.id}}</td>
              <td>{{item.userName}}</td>
              <td>{{item.bossUserId}}</td>
              <td>{{item.currency}}</td>
              <td>{{item.userAddress}}</td>
              <td>{{supportFn(item.supportDeposit)}} </td>
              <td>{{supportFn(item.supportDraw)}}</td>
              <td v-html="timeStrDate(item.createAtStr)" class="nowrap"></td>
              <td v-html="timeStrDate(item.updateAtStr)" class="nowrap"></td>
              <td>{{item.depositRate}}</td>
              <td>{{item.drawRate}}</td>
              <td class="btn-ctrl">
                <router-link tag="a" :to="{path: '/trade/bossSupport/bossSupportDetails',query: {id:item.id}}">查看</router-link>
              </td>
            </tr>
            <div style="z-index: 9999" class="web-loading1" v-show="loadShow">
              <img src="~@img/loading.gif" />
            </div>
          </table>
        </div>
      </list-wrap>
    </div>
    <el-dialog width="30%" :visible.sync="showDialog" title="新增老板币种支持">
      <div class="problem-box">
        <label>老板ID</label>
        <div class="dropdown-wrap  input-t">
          <input type="text"  v-model.trim="bossId2" placeholder="请输入老板id">
        </div>
      </div>
      <div class="problem-box">
        <label>币种</label>
        <div class="dropdown-wrap input-t">
          <input type="text" v-model.trim="currency" placeholder="请输入币种">
        </div>
      </div>
      <div class="problem-box">
        <label>用户名</label>
        <div class="dropdown-wrap  input-t">
          <input type="text" v-model.trim="userName" placeholder="请输入用户名">
        </div>
      </div>
      <div class="problem-box">
        <label>充值</label>
        <div class="input-wrap dropdown-wrap">
          <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{ orderTypeText2}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
            <el-dropdown-menu class="menu dialog-input" slot="dropdown">
              <el-dropdown-item :command="composeValue(item, 4)" v-for="(item, index) in orderType" :key="index" v-show="index > 0">{{item.text}}
              </el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </div>
      </div>
      <div class="problem-box">
        <label>提现</label>
        <div class="input-wrap dropdown-wrap">
          <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{stateEnumText2}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
            <el-dropdown-menu class="menu dialog-input" slot="dropdown">
              <el-dropdown-item :command="composeValue(item, 5)" v-for="(item, index) in orderType"  v-show="index > 0" :key="index">{{item.text}}
              </el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </div>
      </div>
      <div class="errMsg" v-show="errTip">{{errTip}}</div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="showDialog = false">取 消</el-button>
        <el-button type="primary" @click="paySure()">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script type="text/ecmascript-6">
/*eslint-disable*/
import Dialog from '@/components/dialog/dialog'

export default {
  data() {
    return {
      loadShow: false,
      bossId2: '',
      userName: '',
      currency: '',
      showDialog: false,
      errTip: '',
      addDeposit: '不支持',
      addDepositId: '',
      bossId: '',
      loadingShow: false,
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
	    state2: 0,
      orderTypeState: '',
	    orderTypeState2: 0,
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
      stateEnumText2: '不支持',
      orderTypeText: '全部',
      orderTypeText2: '不支持',
      callbackEnum: [],
      stateEnum: [],
      orderType: [],
	    sysBossFlagEnum: []
    }
  },
  created() {
    this.getStatus()
  },
  methods: {
	  paySure () {
          if (this.bossId2 === '') {
                this.errTip = '请输入老板ID'
            return
          }
		  if (this.currency === '') {
			  this.errTip = '请输入币种'
			  return
		  }
		  if (this.userName === '') {
			  this.errTip = '请输入用户名'
			  return
		  }
		  this.$fetch.post(`/finance/bossSupport/addBossInfoSupport`, {
			  bossUserId: this.bossId2, //：老板id
			  currency: this.currency, //：币种
			  userName: this.userName, //：用户名
			  supportDeposit: this.orderTypeState2, //：支持充值 0不支持 1支持
			  supportDraw: this.state2, //：支持提现 0不支持 1支持
      }).then(res => {
			  if (res.code === 0) {
			  	this.showDialog = false
				  Dialog({
            content: '操作成功',
            okFn: () => {
	            this.getData(1, 20)
            }
          })
			  }
		  })
    },
    addBossFn () {
      this.showDialog = true
      this.bossId2 = ''
      this.currency  = ''
      this.userName = ''
      this.orderTypeText2 = '不支持'
      this.orderTypeState2 = 0
      this.stateEnumText2 = '不支持'
      this.state2 = 0
    },
    supportFn (type) {
      if (type === 0) return '不支持'
      if (type === 1) return '支持'
    },
    emptyFn() {
      this.state2 = ''
      this.transferId = ''
      this.bossId = ''
      this.startDate = ''
      this.endDate = ''
      this.callbackState = ''
      this.state = ''
      this.orderTypeState = ''
      this.callbackStatus = '全部'
      this.stateEnumText = '全部'
      this.orderTypeText = '全部'
    },
    btnFn(state, callback) {
      if (state === 0) return '取消订单'
      if (callback === 4) return '重新回调'
      return '-'
    },
    btnCtrlFn(id) {
      this.$router.push({
        path: '/trade/bossSupport/bossSupportDetails',
        query: {id}
      })
    },
    // 获取状态
    getStatus() {
      this.$fetch.get(`/public/enumValue?classPackage=finance.BossInfo$BossInfoStatusEnum;finance.BossInfo$BossSupportEnum;finance.BossInfo$SysBossFlagEnum;finance.BossInfo$BossInfoPayStatusEnum;finance.BossInfo$BossBusyStatusEnum;finance.BankInfo$BankTypeEnum;finance.BankInfo$BankStatusEnum;finance.BankInfo$SupportDepositEnum;finance.BossSupportCurrency$SupportEnum;user.User$IsBossEnum&flag=true&state=1`).then(res => {
        if (res.code === 0) {
	        this.$store.commit('SET_BOSSLIST', res.data.data)
          this.sysBossFlagEnum = res.data.data.SysBossFlagEnum
          this.orderType = res.data.data.SupportEnum
        }
      })
    },
    // 获取列表数据
    getData(index, pageSize) {
      if (parseInt(this.total) !== 0) {
        if (pageSize) {
          this.pageSize = pageSize
        }
        let dataObj = {
          pageIndex: index,
          pageSize: this.pageSize
        }
        if (this.transferId) dataObj.id = this.transferId
        if (this.bossId) dataObj.bossUserId = this.bossId
        if (this.orderTypeState > -1) dataObj.supportDeposit = this.orderTypeState
        if (this.state > -1) dataObj.supportDraw = this.state
        if (this.callbackState > -1) dataObj.sysBossFlag = this.callbackState
        if (this.startDate) dataObj.startDateStr = this.$changeDate(this.startDate, '-', 8)
        if (this.endDate) dataObj.endDateStr = this.$changeDate(this.endDate, '-', 8)
        // this.$store.commit('SET_LOADING', true)
        this.loadShow = true
        this.$fetch.post(`/finance/bossSupport/queryBossSupportList`, dataObj).then(res => {
          this.loadShow = false
        	if (res.data.page) {
		        this.listData = res.data.page.list
		        this.totalData = res.data.page.totalCount
          } else {
		        this.listData = []
            this.totalData = 0
          }
        }).catch(err => {
          console.log(err)
          this.loadShow = false
        })
      }
    },
    commandChange (commend) {
      if (commend.index === 1) {
        this.orderTypeText = commend.item.text
        this.orderTypeState = Number(commend.item.id)
      } else if (commend.index === 2) {
        this.stateEnumText = commend.item.text
        this.state = Number(commend.item.id)
      } else if (commend.index === 3) {
        this.callbackStatus = commend.item.text
        this.callbackState = Number(commend.item.id)
      } else if (commend.index === 4) {
        this.orderTypeText2 =  commend.item.text
        this.orderTypeState2 = Number( commend.item.id)
      } else if (commend.index === 5) {
        this.stateEnumText2 = commend.item.text
        this.state2 = Number(commend.item.id)
      }
    },
    search() {
      this.total = 1 // hook listWrap组件的loading
      this.getData(1, 20)
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
    margin-bottom: 20px
    .dropdown-wrap {
      cursor pointer
      text-align center
      line-height 40px
      width: 250px;
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
      &.input-t{
        border:none
        cursor none
        padding: 0
        input{
          border-radius: 6px;
          width: 100%
          height: 100%
          padding: 0 10px
          border: 1px solid #DCDFE6;
        }
      }
    }
    >>>.el-dropdown-menu{
      width 240px!important
    }
  }
  >>>.el-dialog__body{
    padding 30px 20px!important
  }
</style>


