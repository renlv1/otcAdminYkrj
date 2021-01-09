<template>
  <div class="transaction-list">
    <div class="crumb">
      <span>提现订单</span>
    </div>
    <div class="page-wrap a-el-tab">

      <div class="search clearfix">

        <div>
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
              <el-dropdown @command="commandChange">
                <span class="el-dropdown-link">
                  {{orderTypeText}}<i class="el-icon-arrow-down el-icon--right"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu dropdown-menu-hidden" slot="dropdown">
                  <el-dropdown-item :command="composeValue(item, 1)" v-for="(item, index) in payStatusArr" :key="index">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </div>
          <div class="input-item">
            <div class="label">系统标识</div>
            <div class="input-wrap dropdown-input">
              <el-dropdown  @command="commandChange">
                <span class="el-dropdown-link">
                  {{stateEnumText}}<i class="el-icon-arrow-down el-icon--right"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu" slot="dropdown">
                  <el-dropdown-item :command="composeValue(item, 2)" v-for="(item, index) in sysBossFlagArr" :key="index">{{item.text}}</el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </div>
          <div class="input-item">
            <div class="label">状态</div>
            <div class="input-wrap dropdown-input">
              <el-dropdown @command="commandChange">
                <span class="el-dropdown-link">
                  {{callbackStatus}}<i class="el-icon-arrow-down el-icon--right"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu dropdown-menu-hidden2" slot="dropdown">
                  <el-dropdown-item :command="composeValue(item, 3)" v-for="(item, index) in orderStatusArr" :key="index">{{item.text}}</el-dropdown-item>
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
        </div>
        <div class="input-item btn-ctrl-w">
          <div class="btn-ctrl btn-empty" @click="emptyFn">清空</div>
          <div class="btn-ctrl" @click="search">查询</div>
        </div>
      </div>
      <div class="flag-t" v-show="listData.length">待老板打款:{{flagObj.waitBossMoney}};  待用户确认收款:{{flagObj.getAmountByUser}};  已完成:{{flagObj.getFinishAmount}};  仲裁完成:{{flagObj.getZhongcaiAmount}};  仲裁中:{{flagObj.getZhongcaiDoingAmount}}；总打款额:{{flagObj.totalUSD}}USD/{{flagObj.totalCNY}}CNY     <span style="margin-left: 10px;">共{{flagObj.totalAmount}}笔</span></div>
      <list-wrap :list="listData" :total="totalData" @change="getData" :pageSize="pageSize">
        <div class="web-table">
          <table>
            <tr class="thead">
              <td>ID</td>
              <td>用户名</td>
              <td>老板名</td>
              <td>提现金额</td>
              <td>币种</td>
              <td>手续费</td>
              <td>实际收款金额</td>
              <td>订单状态</td>
              <td>支付状态</td>
              <td>用户支付ID</td>
              <td>创建时间</td>
              <td>接单时间</td>
              <td style="width: 120px;">操作</td>
            </tr>
            <tr v-for="(item, index) in isListNull(listData)" :key="index">
              <td>{{item.id}}</td>
              <td>{{item.username}}</td>
              <td>{{item.bossName}}</td>
              <td>{{item.drawAmount}}</td>
              <td>{{item.payCurrency}}</td>
              <td>{{item.feeAmount}}</td>
              <td>{{item.receiveAmount}}</td>
              <td>{{orderStatusText(item.status)}}</td>
              <td>{{payStatusText(item.payStatus)}}</td>
              <td>{{item.userPayTransactionId}}</td>
              <td v-html="timeStrDate(item.createAt)" class="nowrap"></td>
              <td v-html="timeStrDate(item.acceptAt)" class="nowrap"></td>
              <td class="btn-ctrl" style="width: 120px;">
                <router-link tag="a" :to="{path: '/trade/draw/drawDetail',query: {id:item.id}}" >查看</router-link>
                <router-link tag="a" :to="{path: '/trade/draw/audit',query: {id:item.id}}" v-show="item.status === 4" class="btn-2">审核</router-link>
                <span class="btn-2" v-show="stateText(item)" @click="stateFn(item)">{{stateText(item)}}</span>
              </td>
            </tr>
            <div style="z-index: 9999" class="web-loading1" v-show="loadShow">
              <img src="~@img/loading.gif" />
            </div>
          </table>
        </div>
      </list-wrap>
    </div>
    <el-dialog width="30%" :visible.sync="orderDialog">
      <div class="problem-box">
        <img src="../../../assets/img/warning.png"><label>确认待接单？</label>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="orderDialog = false">取 消</el-button>
        <el-button type="primary" @click="paySure(1)">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog width="30%" :visible.sync="daKuangDialog">
      <div class="problem-box">
        <img src="../../../assets/img/warning.png"><label>确认打款？</label>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="daKuangDialog = false">取 消</el-button>
        <el-button type="primary" @click="paySure(2)">确 定</el-button>
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
        loadShow:false,
        daKuangDialog: false,
        orderDialog: false,
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
				pageSize: 10,
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
        orderID: ''
			}
		},
     created() {
			this.getStatus()
      // this.getData()
		},
     methods: {
      paySure(index) {
        let url = ''
        if (index === 1) { url = '/finance/draw/pendingOrder'}
        if (index === 2) { url = '/finance/draw/bossMadeMoney'}
        this.$fetch.post(url,{
          id: this.orderID
        }).then(res => {
          this.orderDialog = false
          this.daKuangDialog = false
          Dialog({
            title: '',
            content: res.msg,
            okFn: () => {
              this.getData(1, 20)
              this.$store.dispatch('getUserInfo')
            }
          })
        })
      },
      stateFn (item) {
        this.orderID = item.id
        // if (item.status === 4) return '审核'
        if (item.status === 2){
          this.orderDialog = true
        } else if (item.payStatus === 3){
          this.daKuangDialog = true
        }
      },
      stateText (item) {
        // if (item.status === 4) return '审核'
        if (item.status === 2) return '待接单'
        if (item.payStatus === 3) return '老板打款'
        return  ''
      },
      handleClick(tab, event) {
				this.tabIndex = Number(tab.index)
				this.emptyFn()
			},
      orderStatusText (type) {
				return this.orderStatusArr[type + 1].text
			},
      payStatusText  (type) {
        return this.payStatusArr[type + 1].text
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
            path: '/trade/draw/drawDetail',
            query: {id}
        })
    },
      // 获取状态
      getStatus() {
        this.$fetch.get(`/public/enumValue?classPackage=finance.DrawRecord$DrawStatusEnum;finance.DrawRecord$DrawPayStatusEnum;&flag=true&state=1`).then(res => {
            if (res.code === 0) {
                this.payStatusArr = res.data.data.DrawPayStatusEnum
                this.orderStatusArr = res.data.data.DrawStatusEnum
            }
        })
    },
      getData(pageIndex, pageSize) {
        this.loadShow = true
        let obj = {
	        pageIndex: pageIndex || this.pageIndex,
	        pageSize: pageSize || this.pageSize,
        }
	      if (this.userId) obj.id = this.userId
	      if (this.userName) obj.username = this.userName
	      if (this.bossName) obj.bossName = this.bossName
	      if (this.state > -1) obj.sysBossFlag = this.state
	      if (this.callbackState > -1) obj.status = this.callbackState
	      if (this.orderTypeState > -1) obj.payStatus = this.orderTypeState
	      if (this.startDate) obj.startAtStr = this.$changeDate(this.startDate, '-', 8)
	      if (this.endDate) obj.endAtStr = this.$changeDate(this.endDate, '-', 8)
        this.$fetch.post('/finance/draw/queryDrawList', obj).then((res) => {
          this.loadShow = false
          this.flagObj = res.data
          if (this.tabIndex === 2) {
            this.isShow = true
          }
          if(res.data.page) {
            this.listData = res.data.page.list
            this.totalData = res.data.page.totalCount
          } else {
            this.listData = []
            this.totalData = 0
          }
        }).catch(err => {
          this.loadShow = false
          console.log(err)
        })
      },
      commandChange (commend) {
         let obj = commend.item
         let id = commend.index
         if (id === 1) {
           this.orderTypeText = obj.text
           this.orderTypeState = Number(obj.id)
         } else if (id === 2) {
           this.stateEnumText = obj.text
           this.state = Number(obj.id)
         } else if (id === 3) {
           this.callbackStatus = obj.text
           this.callbackState = Number(obj.id)
         }
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
  .problem-box{
    display: flex;
    align-items: center;
  }
</style>


