<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb detail-page">
      <div class="title-left">
        <router-link to="/trade/bossInfo" class="title-m">老板信息 </router-link>
        <span> / 老板详情</span>
      </div>
      <div class="btn-right">
        <span class="pay-money" v-show="dataObj.isBoss === 1" @click="applyBossFn">申请老板</span>
        <span class="btn-save btn-blue" @click="saveBank">保存</span>
      </div>
    </div>
    <div class="page-wrap detail-cont">
      <ul class="ul-w ul-input-w">
        <li class="li-item li-item-text">
          <div class="li-left">ID</div>
          <div class="li-right">{{dataObj.id}}</div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">用户名</div>
          <div class="li-right">{{dataObj.userName}}</div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">用户ID</div>
          <div class="li-right">{{dataObj.userId}}</div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">用户地址</div>
          <div class="li-right">{{dataObj.userAddress}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">手机号</div>
          <div class="li-right">
            <input type="text" v-model="mobile">
          </div>
        </li>
        <li class="li-item">
          <div class="li-left">状态</div>
          <div class="li-right">
            <el-dropdown  @command="changeBank1" class="el-dropdown-2">
              <span class="el-dropdown-link">
                {{bossFlagText}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.BossInfoStatusEnum" :key="index" v-show="index > 0">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </li>
        <li class="li-item">
          <div class="li-left">充值</div>
          <div class="li-right">
            <el-dropdown  @command="changeBank2" class="el-dropdown-2">
              <span class="el-dropdown-link">
                {{depositText}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in stateArr"  v-show="index > 0" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </li>
        <li class="li-item">
          <div class="li-left">提现</div>
          <div class="li-right">
            <el-dropdown  @command="changeBank3" class="el-dropdown-2">
              <span class="el-dropdown-link">
                {{drawText}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in stateArr"  v-show="index > 0" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">创建时间</div>
          <div class="li-right">{{dataObj.createAtStr}}</div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">更新时间</div>
          <div class="li-right">{{dataObj.updateAtStr}}</div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">地址</div>
          <div class="li-right">{{isEmptyText(dataObj.location)}}</div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">真实名字</div>
          <div class="li-right">{{isEmptyText(dataObj.realName)}}</div>
        </li>

        <li class="li-item">
          <div class="li-left">系统标识</div>
          <div class="li-right">
            <el-dropdown  @command="changeBank4" class="el-dropdown-2">
              <span class="el-dropdown-link">
                {{sysBossText}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.SysBossFlagEnum"  v-show="index > 0" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </li>
        <li class="li-item">
          <div class="li-left">支付状态</div>
          <div class="li-right">
            <el-dropdown  @command="changeBank5" class="el-dropdown-2">
              <span class="el-dropdown-link">
                {{payStatusText}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.BossInfoPayStatusEnum" v-show="index > 0" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">联系方式</div>
          <div class="li-right">{{mobile}}</div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">保证金支付ID</div>
          <div class="li-right">{{isEmptyText(dataObj.payTransactioId)}}</div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">保证金退还ID</div>
          <div class="li-right">{{isEmptyText(dataObj.callBackTransactionId)}}</div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">充值成功数</div>
          <div class="li-right">{{dataObj.depositCount}}</div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">提现成功数</div>
          <div class="li-right">{{dataObj.drawCount}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">是否老板</div>
          <div class="li-right">
            <el-dropdown  @command="changeBank6" class="el-dropdown-2">
              <span class="el-dropdown-link">
                {{isbossText}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.IsBossEnum" :key="index" v-show="index > 0 && index !== 3">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
  import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return{
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
	      stateId: '',
	      currency: '',
	      isbossId: '',
	      bankType: '',
	      drawTextId2: '',
	      bankId: '',
	      bankName: '',
	      depositText: '',
	      depositId: '',
	      sysBossText: '',
	      isbossText: '',
	      drawText: '',
	      payStatusText: '',
        drawId: '',
        bossFlagText: '',
	      bankArr: this.$store.state.bankList.BankTypeEnum,
	      stateArr: this.$store.state.bossList.BossSupportEnum,
	      options1: [
		      {id: 0, text: '不支持'},
		      {id: 1, text: '支持'},
        ],
        currencyOptins: [
	        {id: 1, text: 'CNY'},
	        {id: 2, text: 'USD'},
        ],
	      username: '',
        errTip: '',
        paymentArr: [
          {text: '给用户打钱',id: 1},
          {text: '给老板打钱',id: 2},
        ],
        showDialog: false,
        id: this.$route.query.id,
        data: '',
        payText: '',
        payType: '',
        dataObj: {},
	      mobile: '',
      }
    },
    created() {
    	this.dataObj = JSON.parse(this.$route.query.obj)
      this.bossType = this.dataObj.status
      this.mobile = this.dataObj.mobile
      this.depositText = this.$store.state.bossList.BossSupportEnum[this.dataObj.supportDeposit + 1].text
      this.stateId = this.dataObj.supportDeposit
      this.drawText = this.$store.state.bossList.BossSupportEnum[this.dataObj.supportDraw + 1].text
      this.drawTextId2 = this.dataObj.supportDraw
      this.sysBossText = this.$store.state.bossList.SysBossFlagEnum[this.dataObj.sysBossFlag + 1].text
      this.depositId = this.dataObj.sysBossFlag
      this.isbossText = this.$store.state.bossList.IsBossEnum[this.dataObj.isBoss].text
	    this.isbossId = this.dataObj.isBoss
      this.payStatusText = this.$store.state.bossList.BossInfoPayStatusEnum[this.dataObj.payStatus].text
      this.drawId = this.dataObj.payStatus
      this.bossFlagText = this.$store.state.bossList.BossInfoStatusEnum[this.dataObj.status].text
      this.bankId = this.dataObj.status
    },
    methods: {
	    applyBossFn () {
		    Dialog({
			    content: '确认申请老板？',
			    okFn: () =>{
				    this.$fetch.post(`/finance/boss/applyBoss`, {
					    id: this.dataObj.id
				    }).then(res => {
					    if (res.code === 0) {
						    Dialog({
							    content: '操作成功',
							    okFn: () => {
								    this.$router.push('/trade/bossInfo')
							    }
						    })
					    }
				    })
			    }
		    })
	    },
	    saveBank () {
	    	Dialog({
			    content: '是否保存该数据？',
          okFn: () => {
	          let obj = {
		          id: this.dataObj.id,
		          mobile: this.mobile,
		          status: this.bankId,
		          supportDeposit: this.stateId,
		          supportDraw: this.drawTextId2,
		          sysBossFlag: this.depositId,
		          payStatus: this.drawId,
		          isBoss: this.isbossId
	          }
	          this.$fetch.post('/finance/boss/updateBossInfo', obj).then(res => {
		          if (res.code === 0) {
			          Dialog({
				          content: '保存成功',
				          okFn: () => {
					          this.$router.push('/trade/bossInfo')
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
	    },
	    changeBank3 (v) {
		    this.drawText = v.text
        this.drawTextId2 = v.id
	    },
	    changeBank4 (v) {
		    this.sysBossText = v.text
        this.depositId = v.id
	    },
	    changeBank5 (v) {
		    this.payStatusText = v.text
        this.drawId = v.id
	    },
	    changeBank6 (v) {
		    this.isbossText = v.text
		    this.isbossId = v.id
	    },
      // 打钱
      handleCommand5(command) {
        this.errTip = ''
        this.payText = command.text
        this.payType = command.id
      }
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
      border-radius: 2px;
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
