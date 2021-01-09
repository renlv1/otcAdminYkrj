<template>
  <div class="transaction-list">
    <div class="crumb list-crumb">
      <span>老板信息</span>
      <div class="btn-right">
        <span class="btn-save" @click="$router.push('/trade/bossInfo/addBoss')">+ 新增</span>
      </div>
    </div>
    <div class="page-wrap">
      <div class="search clearfix">
        <div class="input-item">
          <div class="label">ID</div>
          <div class="input-wrap"><input type="number" v-model="bossId"></div>
        </div>
        <div class="input-item">
          <div class="label">老板名</div>
          <div class="input-wrap"><input type="text" v-model.trim="bossName"></div>
        </div>
        <div class="input-item">
          <div class="label">状态</div>
          <div class="input-wrap dropdown-input">
            <el-dropdown @command="commandChange">
            <span class="el-dropdown-link">
              {{bossFlag}}<i class="el-icon-arrow-down el-icon--right"></i>
            </span>
              <el-dropdown-menu class="dropdown-menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 1)" v-for="(item, index) in BossInfoStatusEnum" :key="index">{{item.text}}
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
              {{bossStatus}}<i class="el-icon-arrow-down el-icon--right"></i>
            </span>
              <el-dropdown-menu class="dropdown-menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 2)" v-for="(item, index) in SysBossFlagEnum" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="input-item">
          <div class="label">充值</div>
          <div class="input-wrap dropdown-input">
            <el-dropdown @command="commandChange">
            <span class="el-dropdown-link">
              {{depositStatus}}<i class="el-icon-arrow-down el-icon--right"></i>
            </span>
              <el-dropdown-menu class="dropdown-menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 3)" v-for="(item, index) in BossSupportEnum" :key="index">{{item.text}}
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
              {{drawStatus}}<i class="el-icon-arrow-down el-icon--right"></i>
            </span>
              <el-dropdown-menu class="dropdown-menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 4)" v-for="(item, index) in BossSupportEnum" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="input-item">
          <div class="label">支付状态</div>
          <div class="input-wrap dropdown-input">
            <el-dropdown @command="commandChange">
            <span class="el-dropdown-link">
              {{callbackStatus}}<i class="el-icon-arrow-down el-icon--right"></i>
            </span>
              <el-dropdown-menu class="dropdown-menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 5)" v-for="(item, index) in payStatusArr" :key="index">{{item.text}}
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
                    v-model="endDate"
                    default-time="23:59:59"
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
              <td>ID</td>
              <td>用户名</td>
              <td>老板名</td>
              <td>是否老板</td>
              <td style="width: 100px;">用户地址</td>
              <td>状态</td>
              <td>支付状态</td>
              <td>充值</td>
              <td>提现</td>
              <td style="width: 95px;">创建时间</td>
              <td style="width: 95px;">更新时间</td>
              <td>系统标识</td>
              <td style="width: 120px;" >操作</td>
            </tr>
            <tr  v-for="(item, index) in isListNull(listData)" :key="index">
              <td>{{item.id}}</td>
              <td>{{item.userName}}</td>
              <td>{{item.bossName}}</td>
              <td>{{bossFn(item.isBoss)}}</td>
              <td class="text-left">{{item.userAddress}}</td>
              <td>{{statusFn(item.status)}}</td>
              <td>{{payStatusFn(item.payStatus)}}</td>
              <td>{{depositFn(item.supportDeposit)}}</td>
              <td>{{depositFn(item.supportDraw)}}</td>
              <td v-html="timeStrDate(item.createAtStr)" class="nowrap text-left">{{item.createAtStr}}</td>
              <td v-html="timeStrDate(item.updateAtStr)" class="nowrap text-left">{{item.updateAtStr}}</td>
              <td>{{bossFlagFn(item.sysBossFlag)}}</td>
              <td class="btn-ctrl" style="width: 120px;" >
                <router-link tag="a" :to="{path: '/trade/bossInfo/bossInfoDetails', query: {obj: JSON.stringify(item)}}">查看</router-link>
                <span v-show="item.isBoss === 1" class="btn-2" @click="applyBossFn(item)">申请老板</span>
              </td>
            </tr>
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
      loadingShow: false,
      searchId: '',
      dialogFormVisible: false,
      pageSize: 20,
      totalData: 0,
      dialogVisible: false,
      listData: [],
      bossId: '',
      bossName: '',
      startDate: '',
      endDate: '',
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
      bossFlag: '全部',
      bossFlagId: '',
      drawStatus: '全部',
      drawId: '',
      depositStatus: '全部',
      depositId: '',
      callbackStatus: '全部',
      payId: '',
      bossStatus: '全部',
      bossStatusId: '',
      callbackEnum: [],
      stateEnum: [],
      orderType: [],
      isBossEnum: [],
      payStatusArr: [],
      BossInfoStatusEnum: [],
      BossSupportEnum: [],
      SysBossFlagEnum: []
    }
  },
  created() {
    this.getStatus()
  },
  methods: {
	  applyBossFn (item) {
	  	Dialog({
			  content: '确认申请老板？',
        okFn: () =>{
	        this.$fetch.post(`/finance/boss/applyBoss`, {
	        	id: item.id
          }).then(res => {
          	if (res.code === 0) {
          		Dialog({
                content: '操作成功',
                okFn: () => {
	                this.getData(1,  20)
                }
              })
            }
	        })
        }
      })
    },
	  viewFn (item) {
	  	this.$router.push({
        path: '/trade/bossInfo/bossInfoDetails',
        query: {obj: JSON.stringify(item)}
      })
	  },
  	bossFn (v) {
	  	if (this.isBossEnum) return this.isBossEnum[v].text
    },
	  statusFn (v) {
		  return this.BossInfoStatusEnum[v].text
    },
	  payStatusFn (v) {
  		return this.payStatusArr[v].text
    },
	  depositFn (v) {
		  return this.BossSupportEnum[v + 1].text
    },
    bossFlagFn (v) {
  		return this.SysBossFlagEnum[v + 1].text
    },

    emptyFn() {
      this.bossId = ''
      this.bossName = ''
      this.startDate = ''
      this.endDate = ''
      this.callbackStatus = '全部'
      this.bossStatus = '全部'
      this.bossFlag = '全部'
      this.bossFlagId = ''
      this.drawStatus = '全部'
      this.drawId = ''
      this.depositStatus = '全部'
      this.depositId = ''
      this.payId = ''
      this.bossStatusId = ''
    },
    btnCtrlFn(state, id, callback) {
    },
    // 获取状态
    getStatus() {
      this.$fetch.get(`/public/enumValue?classPackage=finance.BossInfo$BossInfoStatusEnum;finance.BossInfo$BossSupportEnum;finance.BossInfo$SysBossFlagEnum;finance.BossInfo$BossInfoPayStatusEnum;finance.BossInfo$BossBusyStatusEnum;finance.BankInfo$BankTypeEnum;finance.BankInfo$BankStatusEnum;finance.BankInfo$SupportDepositEnum;finance.BossSupportCurrency$SupportEnum;user.User$IsBossEnum&flag=true&state=1`).then(res => {
        if (res.code === 0) {
          this.$store.commit('SET_BOSSLIST', res.data.data)
          let arr = res.data.data
          this.isBossEnum = arr.IsBossEnum
          this.BossInfoStatusEnum = arr.BossInfoStatusEnum
          this.payStatusArr = arr.BossInfoPayStatusEnum
          this.BossSupportEnum = arr.BossSupportEnum
          this.SysBossFlagEnum = arr.SysBossFlagEnum
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
        if (this.bossId) dataObj.id = this.bossId
        if (this.bossName) dataObj.bossName = this.bossName
        if (this.bossFlagId) dataObj.status = this.bossFlagId
        if (this.bossStatusId > -1) dataObj.sysBossFlag = this.bossStatusId
        if (this.depositId > -1) dataObj.supportDeposit = this.depositId
        if (this.drawId > -1) dataObj.supportDraw = this.drawId
        if (this.payId) dataObj.payStatus = this.payId
        if (this.startDate) dataObj.startDateStr = this.$changeDate(this.startDate, '-', 8)
        if (this.endDate) dataObj.endDateStr = this.$changeDate(this.endDate, '-', 8)
        this.$store.commit('SET_LOADING', true)
        this.$fetch.post(`/finance/boss/queryBossInfoList`, dataObj).then(res => {
	        this.$store.commit('SET_LOADING', false)
          if(res.data.page) {
            this.listData = res.data.page.list
            this.totalData = res.data.page.totalCount
          } else {
            this.listData = []
          }
        }).catch(err => {
	        this.$store.commit('SET_LOADING', false)
        })
      }
    },
    commandChange (commend) {
      let item = commend.item
      let num = commend.index
      if (num === 1) {
        this.bossFlag = item.text
        this.bossFlagId = Number(item.id)
      } else if (num === 2) {
        this.bossStatus = item.text
        this.bossStatusId = Number(item.id)
      } else if (num === 3) {
        this.depositStatus = item.text
        this.depositId = Number(item.id)
      } else if (num === 4) {
        this.drawStatus = item.text
        this.drawId = Number(item.id)
      } else if (num === 5) {
        this.callbackStatus = item.text
        this.payId = Number(item.id)
      }
    },
    search() {
      this.total = 1 // hook listWrap组件的loading
      this.getData(1,  20)
    },
  }
}
</script>

<style scoped lang="less">
  .list-crumb{
    display: flex;
    align-items: center;
    justify-content: space-between;
    background-color: #f3f6fc;
    margin-bottom: 20px;
    margin-top: -13px;
    .btn-right{
      position: static;
    }
  }
</style>

