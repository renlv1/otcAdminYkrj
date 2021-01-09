<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb detail-page">
      <div class="title-left">
        <router-link to="/trade/draw" class="title-m">提现订单 </router-link>
        <span> / 提现订单详情</span>
      </div>
      <div class="btn-right">
        <span class="btn-save btn-blue" @click="saveBank">保存</span>
      </div>
    </div>
    <div class="page-wrap detail-cont">
<!--     1 订单信息-->
      <div class="page-item">
        <div class="title-a">
          <div class="solid"></div>
          <span>订单信息</span>
        </div>
        <ul class="ul-input-w ul-w flex-ul-w">
          <li class="li-item">
            <div class="li-left">ID</div>
            <div class="li-right li-right-text">{{dataObj.id}}</div>
          </li>
          <li class="li-item">
            <div class="li-left">用户名</div>
            <div class="li-right">
              <input type="text" v-model.trim="username">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">用户ID</div>
            <div class="li-right">
              <input type="text" v-model.trim="userId">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">用户地址</div>
            <div class="li-right li-right-text">{{dataObj.userAddress}}</div>
          </li>
          <li class="li-item">
            <div class="li-left">老板名</div>
            <div class="li-right">
              <input type="text" v-model.trim="dataObj.bossName">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">老板ID</div>
            <div class="li-right">
              <input type="text" v-model.trim="dataObj.bossUserId">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">老板地址</div>
            <div class="li-right li-right-text">{{isEmptyText(dataObj.bossAddress)}}</div>
          </li>
          <li class="li-item">
            <div class="li-left">提现金额</div>
            <div class="li-right">
              <input type="text" v-model.trim="dataObj.drawAmount">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">提现币种</div>
            <div class="li-right">
              <input type="text" v-model.trim="dataObj.drawCurrency">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">提现汇率</div>
            <div class="li-right li-right-text">{{isEmptyText(dataObj.drawrate)}}</div>
          </li>
          <li class="li-item">
            <div class="li-left">手续费率</div>
            <div class="li-right">
              <input type="text" v-model.trim="dataObj.feeRate">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">手续费</div>
            <div class="li-right">
              <input type="text" v-model.trim="dataObj.feeAmount">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">实际收款金额</div>
            <div class="li-right">
              <input type="text" v-model.trim="dataObj.receiveAmount">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">订单状态</div>
            <div class="li-right">
              <el-dropdown  @command="changeBank1" class="el-dropdown-2">
                <span class="el-dropdown-link">
                  {{bossFlagText}}<i class="el-icon-arrow-down"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu-details dropdown-menu-hidden2" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in orderStatusArr" :key="index" v-show="index > 0">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">支付状态</div>
            <div class="li-right">
              <el-dropdown  @command="changeBank2" class="el-dropdown-2">
                <span class="el-dropdown-link">
                  {{depositText}}<i class="el-icon-arrow-down"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu-details dropdown-menu-hidden2" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in payStatusArr" :key="index" v-show="index > 0">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">订单来源</div>
            <div class="li-right li-right-text">{{sourceFn(dataObj.source)}}</div>
          </li>
          <li class="li-item">
            <div class="li-left">审核说明</div>
            <div class="li-right">
              <input type="text" v-model.trim="dataObj.auditDesc">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">创建时间</div>
            <div class="li-right li-right-text">{{dataObj.createAt}}</div>
          </li>
          <li class="li-item">
            <div class="li-left">更新时间</div>
            <div class="li-right li-right-text">{{isEmptyText(dataObj.updateAt)}}</div>
          </li>
          <li class="li-item">
            <div class="li-left">接单时间</div>
            <div class="li-right li-right-text">{{isEmptyText(dataObj.acceptAt)}}</div>
          </li>
          <li class="li-item">
            <div class="li-left li-left2">提现成功老板到账ID</div>
            <div class="li-right">
              <input type="text" v-model.trim="dataObj.drawTrsactionId">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left li-left2">提现失败退回用户ID</div>
            <div class="li-right">
              <input type="text" v-model.trim="dataObj.failTransactionId">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left li-left-label">用户支付ID</div>
            <div class="li-right">
              <input type="text" v-model.trim="dataObj.userPayTransactionId">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">备注</div>
            <div class="li-right">
              <input type="text" v-model.trim="dataObj.remark">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">申诉问题ID</div>
            <div class="li-right">
              <input type="text" v-model.trim="dataObj.problemId">
            </div>
          </li>
        </ul>
      </div>
<!--     2 打款银行卡信息-->
      <div class="page-item">
        <div class="title-a">
          <div class="solid"></div>
          <span>打款银行卡信息</span>
        </div>
        <ul class="ul-input-w ul-w flex-ul-w">
          <li class="li-item">
            <div class="li-left">打款银行ID</div>
            <div class="li-right">
              <input type="text" v-model.trim="dataObj.bankInfoId">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">打款银行名称</div>
            <div class="li-right">
              <input type="text" v-model.trim="drawBankName">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">打款户名</div>
            <div class="li-right">
              <input type="text" v-model.trim="drawRealName">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">打款帐号</div>
            <div class="li-right">
              <input type="text" v-model.trim="drawUserName">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">SWIFT</div>
            <div class="li-right">
              <input type="text" v-model.trim="bossSwift">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">IBAN</div>
            <div class="li-right">
              <input type="text" v-model.trim="bossIban">
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
  import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return{
	      payStatusArr: [],
	      orderStatusArr: [],
	      depositRate: '',
	      drawRate: '',
	      bossType: '',
	      bankAddress: '',
	      country: '',
	      bankNumber: '',
	      iban: '',
	      swift: '',
	      realName: '',
	      subBankName: '',
	      userAddress: '',
	      userId: '',
	      stateText: '',
	      currency: '',
	      bankType: '',
	      bankName: '',
	      depositText: '',
	      stateId: '',
	      sysBossText: '',
	      depositId: '',
	      isbossText: '',
	      isbossId: '',
	      isbossText7: 'CNY',
	      isbossText8: '',
	      isbossId8: '',
	      isbossText9: 'CNY',
	      isbossText10: '',
	      isbossId10: '',
	      isbossText11: '',
	      isbossId11: '',
	      isbossText12: '',
	      isbossId12: '',
	      drawText: '',
	      drawTextId2: '',
	      payStatusText: '',
        drawId: '',
        bossFlagText: '',
	      bankId: '',
	      options1: [
		      {id: 0, text: '不支持'},
		      {id: 1, text: '支持'},
        ],
        currencyOptins: [
	        {text: 'CNY'},
	        {text: 'USD'},
	        {text: 'BTC'},
        ],
	      username: '',
        showDialog: false,
        id: this.$route.query.id,
        data: '',
        payText: '',
        payType: '',
        dataObj: {},
	      mobile: '',
	      bossSwift: '',
	      bossIban: '',
	      bossBankAccount: '',
	      drawBankName: '',
	      drawRealName: '',
	      drawUserName: ''
      }
    },
    created() {
    	this.getDetails()
    },
    methods: {
	    // 获取状态
	    getStatus() {
		    this.$fetch.get(`/public/enumValue?classPackage=finance.DrawRecord$DrawStatusEnum;finance.DrawRecord$DrawPayStatusEnum;&flag=false&state=1`).then(res => {
			    if (res.code === 0) {
				    this.payStatusArr = res.data.data.DrawPayStatusEnum
				    this.orderStatusArr = res.data.data.DrawStatusEnum
				    this.bossFlagText = this.orderStatusArr[this.bankId].text
				    this.depositText = this.payStatusArr[this.stateId].text
			    }
		    })
	    },
	    sourceFn (status) {//1:秘密 2：克莱英瓶
		    if (status === 1) return '秘密'
		    if (status === 2) return '克莱英瓶'
        return '-'
      },
    	getDetails () {
    		// /finance/deposit/getDepositMessage
        this.$fetch.post('/finance/draw/getDrawMessage', {
        	id: this.id
        }).then(res => {
        	if (res.code === 0) {
        		let obj = res.data.resultData
        		this.dataObj = res.data.resultData
		        this.username = obj.username
            this.userId = obj.userId
            this.userAddress = obj.userAddress
            this.bossBankAccount = JSON.parse(obj.drawBankAccount)
            if (this.bossBankAccount) {
	            this.bossSwift = this.bossBankAccount.swift
	            this.bossIban = this.bossBankAccount.iban
              this.drawBankName = this.bossBankAccount.bankname
              this.drawRealName = this.bossBankAccount.realname
              this.drawUserName = this.bossBankAccount.username
            }
            this.stateId = obj.payStatus
            this.bankId = obj.status
		        this.getStatus()
          }
        })
      },
	    saveBank () {
	    	Dialog({
			    content: '是否保存该数据？',
          okFn: () => {
			    	let drawBankAccount = {
					    number: this.bossBankAccount.number,
					    iban: this.bossIban,
					    subbank: this.bossBankAccount.subbank,
					    subbankaddress: this.bossBankAccount.subbankaddress,
					    bankname: this.drawBankName,
					    realname: this.drawRealName,
					    swift: this.bossSwift
				    }
			    	let obj = {	// = 老板信息 ==
			    		id: this.dataObj.id,
					    userId: this.dataObj.userId, //：用户id
              username: this.username, //：用户名
              bossUserId: this.dataObj.bossUserId, //： 老板id
              bossName: this.dataObj.bossName, //： 老板用户名
              drawAmount: this.dataObj.drawAmount, //： 提现金额
              drawCurrency: this.dataObj.drawCurrency, //： 充值币种
              feeRate: this.dataObj.feeRate, //： 手续费率
              feeAmount: this.dataObj.feeAmount, //：手续费
              receiveAmount: this.dataObj.receiveAmount, //： 实际收款金额
              bankInfoId: this.dataObj.bankInfoId, //： 银行id
              drawBankAccount: JSON.stringify(drawBankAccount),
              status: this.bankId, //： 状态
              payStatus: this.stateId, // : 收款状态
              auditDesc: this.dataObj.auditDesc, //： 审核说明
              drawTrsactionId: this.dataObj.drawTrsactionId, //:提现成功给boss到帐id
              failTransactionId: this.dataObj.failTransactionId, //: 提现失败退回用户id
              userPayTransactionId: this.dataObj.userPayTransactionId, //: 用户支付id
              remark: this.dataObj.remark, //： 备注
				    }
	          this.$fetch.post('/finance/draw/updateDrawRecord', obj).then(res => {
		          if (res.code === 0) {
			          Dialog({
				          content: '保存成功',
				          okFn: () => {
					          this.$router.push('/trade/draw')
				          }
			          })
		          }
	          })
          }
        })
      },
	    changeBank1 (v) {
	    	this.bossFlagText = v.text
        this.bankId = v.id
      },
	    changeBank2 (v) {
		    this.depositText = v.text
        this.stateId = v.id
	    }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
  .page-item{
    border-bottom: 1px solid #e5e8ed
    padding-top: 30px
  }
  .detail-page{
    .title-left{
      padding-left: 20px
    }
  }
  .title-a{
    font-size: 16px
    display: flex
    align-items center
    padding-left: 20px
    .solid{
      margin-right: 6px
      width: 10px
      height: 10px
      border-radius 50%
      background-color: #000
    }
  }
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

  .zoom-enter-to{
    transition: none;
  }
</style>
