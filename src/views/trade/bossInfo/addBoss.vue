<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb detail-page">
      <div class="title-left">
        <router-link to="/trade/bossInfo" class="title-m">老板信息 </router-link>
        <span> / 新增</span>
      </div>
      <div class="btn-right">
        <span class="btn-save btn-blue" @click="saveBank">保存</span>
      </div>
    </div>
    <div class="page-wrap detail-cont">
<!--     1 老板信息-->
      <div class="page-item">
        <div class="title-a">
          <div class="solid"></div>
          <span>老板信息</span>
        </div>
        <ul class="ul-input-w ul-w flex-ul-w">
          <li class="li-item">
            <div class="li-left">用户名</div>
            <div class="li-right">
              <input type="text" v-model.trim="username">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">手机号</div>
            <div class="li-right">
              <input type="text" v-model.trim="mobile">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">状态</div>
            <div class="li-right">
              <el-dropdown trigger="click" @command="changeBank1" class="el-dropdown-2">
                <span class="el-dropdown-link">
                  {{bossFlagText}}<i class="el-icon-arrow-down"></i>
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
              <el-dropdown trigger="click" @command="changeBank2" class="el-dropdown-2">
                <span class="el-dropdown-link">
                  {{depositText}}<i class="el-icon-arrow-down"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.BossSupportEnum" :key="index" v-show="index > 0">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">提现</div>
            <div class="li-right">
              <el-dropdown trigger="click" @command="changeBank3" class="el-dropdown-2">
                <span class="el-dropdown-link">
                  {{drawText}}<i class="el-icon-arrow-down"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.BossSupportEnum" :key="index" v-show="index > 0">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">系统标识</div>
            <div class="li-right">
              <el-dropdown trigger="click" @command="changeBank4" class="el-dropdown-2">
                <span class="el-dropdown-link">
                  {{sysBossText}}<i class="el-icon-arrow-down"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.SysBossFlagEnum" :key="index" v-show="index > 0">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">支付状态</div>
            <div class="li-right">
              <el-dropdown trigger="click" @command="changeBank5" class="el-dropdown-2">
                <span class="el-dropdown-link">
                  {{payStatusText}}<i class="el-icon-arrow-down"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.BossInfoPayStatusEnum" :key="index" v-show="index > 0">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </li>
        </ul>
      </div>
<!--     2 银行卡信息-->
      <div class="page-item">
        <div class="title-a">
          <div class="solid"></div>
          <span>银行卡信息</span>
        </div>
        <ul class="ul-input-w ul-w flex-ul-w">
          <li class="li-item">
            <div class="li-left">银行卡类型</div>
            <div class="li-right">
              <el-dropdown trigger="click" @command="changeBank12" class="el-dropdown-2">
                <span class="el-dropdown-link">
                  {{isbossText12}}<i class="el-icon-arrow-down"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.BankTypeEnum" :key="index" v-show="index > 0">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">银行名称</div>
            <div class="li-right">
              <input type="text" v-model.trim="bankName">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">开户支行名称</div>
            <div class="li-right">
              <input type="text" v-model.trim="subBankName">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">银行卡户名</div>
            <div class="li-right">
              <input type="text" v-model.trim="realName">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">银行卡号</div>
            <div class="li-right">
              <input type="text" v-model.trim="bankNumber">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">国家</div>
            <div class="li-right">
              <input type="text" v-model.trim="country">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">SWIFT</div>
            <div class="li-right">
              <input type="text" v-model.trim="swift">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">IBAN</div>
            <div class="li-right">
              <input type="text" v-model.trim="iban">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">银行地址</div>
            <div class="li-right">
              <input type="text" v-model.trim="bankAddress">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">状态</div>
            <div class="li-right">
              <el-dropdown trigger="click" @command="changeBank6" class="el-dropdown-2">
                <span class="el-dropdown-link">
                  {{isbossText}}<i class="el-icon-arrow-down"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.BankStatusEnum" :key="index" v-show="index > 0">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">币种</div>
            <div class="li-right">
              <el-dropdown trigger="click" @command="changeBank7" class="el-dropdown-2">
                <span class="el-dropdown-link">
                  {{isbossText7}}<i class="el-icon-arrow-down"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in currencyOptins" :key="index">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">接单充值</div>
            <div class="li-right">
              <el-dropdown trigger="click" @command="changeBank8" class="el-dropdown-2">
                <span class="el-dropdown-link">
                  {{isbossText8}}<i class="el-icon-arrow-down"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.BossSupportEnum" :key="index" v-show="index > 0">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </li>
        </ul>
      </div>
<!--      老板支付提现与充值币种-->
      <div class="page-item">
        <div class="title-a">
          <div class="solid"></div>
          <span>老板支付提现与充值币种</span>
        </div>
        <ul class="ul-input-w ul-w flex-ul-w">
          <li class="li-item">
            <div class="li-left">币种</div>
            <div class="li-right">
              <el-dropdown trigger="click" @command="changeBank9" class="el-dropdown-2">
                <span class="el-dropdown-link">
                  {{isbossText9}}<i class="el-icon-arrow-down"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in currencyOptins" :key="index">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">用户地址</div>
            <div class="li-right">
              <input type="text" v-model="userAddress">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">充值</div>
            <div class="li-right">
              <el-dropdown trigger="click" @command="changeBank10" class="el-dropdown-2">
                <span class="el-dropdown-link">
                  {{isbossText10}}<i class="el-icon-arrow-down"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.BossSupportEnum" :key="index" v-show="index > 0">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </li>
          <li class="li-item">
            <div class="li-left">提现</div>
            <div class="li-right">
              <el-dropdown trigger="click" @command="changeBank11" class="el-dropdown-2">
                <span class="el-dropdown-link">
                  {{isbossText11}}<i class="el-icon-arrow-down"></i>
                </span>
                <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.BossSupportEnum" :key="index" v-show="index > 0">{{item.text}}
                  </el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </li>
          <li class="li-item">
            <div class="li-left li-left-label">充值费率(暂时不用老板自己设置)</div>
            <div class="li-right">
              <input type="text" v-model.trim="depositRate">
            </div>
          </li>
          <li class="li-item">
            <div class="li-left li-left-label">提现费率(暂时不用老板自己设置)</div>
            <div class="li-right">
              <input type="text" v-model.trim="drawRate">
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
	      depositText: this.$store.state.bossList.BossSupportEnum[1].text,
	      stateId:this.$store.state.bossList.BossSupportEnum[1].id,
	      sysBossText: this.$store.state.bossList.SysBossFlagEnum[1].text,
	      depositId: this.$store.state.bossList.SysBossFlagEnum[1].id,
	      isbossText: this.$store.state.bossList.BankStatusEnum[1].text,
	      isbossId: this.$store.state.bossList.BankStatusEnum[1].id,
	      isbossText7: 'CNY',
	      isbossText8: this.$store.state.bossList.BossSupportEnum[1].text,
	      isbossId8: this.$store.state.bossList.BossSupportEnum[1].id,
	      isbossText9: 'CNY',
	      isbossText10: this.$store.state.bossList.BossSupportEnum[1].text,
	      isbossId10: this.$store.state.bossList.BossSupportEnum[1].id,
	      isbossText11: this.$store.state.bossList.BossSupportEnum[1].text,
	      isbossId11: this.$store.state.bossList.BossSupportEnum[1].id,
	      isbossText12: this.$store.state.bossList.BankTypeEnum[1].text,
	      isbossId12: this.$store.state.bossList.BankTypeEnum[1].id,
	      drawText: this.$store.state.bossList.BossSupportEnum[1].text,
	      drawTextId2: this.$store.state.bossList.BossSupportEnum[1].id,
	      payStatusText: this.$store.state.bossList.BossInfoPayStatusEnum[1].text,
        drawId: this.$store.state.bossList.BossInfoPayStatusEnum[1].id,
        bossFlagText: this.$store.state.bossList.BossInfoStatusEnum[1].text,
	      bankId: this.$store.state.bossList.BossInfoStatusEnum[1].id,
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
      }
    },
    created() {
    },
    methods: {
	    saveBank () {
	    	Dialog({
			    content: '是否保存该数据？',
          okFn: () => {
			    	let arr = []
			    	let obj1 = {	// = 老板信息 ==
					    bossName: this.username,
					    mobile: this.mobile,
					    status: this.bankId,
					    supportDeposit: this.stateId,
					    supportDraw: this.drawTextId2,
					    sysBossFlag: this.depositId,
					    payStatus: this.drawId,
            }
            let obj2 = {
	            bankType: this.isbossId12,
	            bankName: this.bankName,
	            subBankName: this.subBankName,
	            realName: this.realName,
	            bankNumber: this.bankNumber,
	            country: this.country,
	            swift: this.swift,
	            iban: this.iban,
	            bankAddress: this.bankAddress,
	            status: this.isbossId,
	            currency: this.isbossText7,
	            supportDeposit: this.isbossId8,
            }
	          let obj3 = {
              // == 老板支付提现与充值币种 ==
		          currency: this.isbossText9,
		          userAddress: this.userAddress,
		          supportDeposit: this.isbossId10,
		          supportDraw: this.isbossId11,
		          depositRate: this.depositRate,
		          drawRate: this.drawRate
	          }
	          arr.push(obj1,obj2,obj3)
	          this.$fetch.post('/finance/boss/addBossInfo', {
	          	mate: JSON.stringify(arr)
            }).then(res => {
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
	    changeBank7 (v) {
		    this.isbossText7 = v.text
	    },
	    changeBank8 (v) {
		    this.isbossText8 = v.text
		    this.isbossId8 = v.id
	    },
	    changeBank9 (v) {
		    this.isbossText9 = v.text
	    },
	    changeBank10 (v) {
		    this.isbossText10 = v.text
		    this.isbossId10 = v.id
	    },
	    changeBank11 (v) {
		    this.isbossText11 = v.text
		    this.isbossId11 = v.id
	    },
	    changeBank12 (v) {
		    this.isbossText12 = v.text
		    this.isbossId12 = v.id
	    },
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
