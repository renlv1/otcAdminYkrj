<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb detail-page">
      <div class="title-left">
        <router-link to="/trade/bossSupport" class="title-m">老板币种支持</router-link>
        <span> / 老板币种支持详情</span>
      </div>
      <div class="btn-right">
        <span class="btn-save btn-blue" @click="saveBank">保存</span>
      </div>
    </div>
    <div class="page-wrap detail-cont">
      <ul class="ul-w ul-input-w">
        <li class="li-item li-item-text">
          <div class="li-left">ID</div>
          <div class="li-right">{{data.id}}</div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">老板ID</div>
          <div class="li-right">{{data.bossUserId}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">币种</div>
          <div class="li-right">
            <input type="text" v-model="currency">
          </div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">用户名</div>
          <div class="li-right">{{data.userName}}</div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">用户地址</div>
          <div class="li-right">{{data.userAddress}}</div>
        </li>
        <li class="li-item">
          <div class="li-left">充值</div>
          <div class="li-right">
            <el-dropdown @command="changeBank1" class="el-dropdown-2">
              <span class="el-dropdown-link">
                {{bankType}}<i class="el-icon-arrow-down"></i>
              </span>
              <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.SupportEnum" :key="index" v-show="index > 0">{{item.text}}</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </li>
        <li class="li-item">
          <div class="li-left">提现</div>
          <div class="li-right">
            <el-dropdown @command="changeBank2" class="el-dropdown-2">
              <span class="el-dropdown-link">
                {{stateText}}<i class="el-icon-arrow-down"></i>
              </span>
              <el-dropdown-menu class="dropdown-menu-details" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in $store.state.bossList.SupportEnum" :key="index" v-show="index > 0">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">创建时间</div>
          <div class="li-right">{{data.createAt}}</div>
        </li>
        <li class="li-item li-item-text">
          <div class="li-left">更新时间</div>
          <div class="li-right">{{data.updateAt}}</div>
        </li>
        <li class="li-item li-item-text line-height2">
          <div class="li-left">充值费率(暂时不用老板自己设置)</div>
          <div class="li-right">{{data.depositRate}}</div>
        </li>
        <li class="li-item li-item-text line-height2">
          <div class="li-left">提现费率(暂时不用老板自己设置)</div>
          <div class="li-right">{{data.drawRate}}</div>
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
	      bossUserId: '',

	      userAddress: '',
	      userId: '',
	      stateText: '',
	      stateId: '',
	      currency: '',
	      bankType: '',
	      bankId: '',
	      bankName: '',
	      depositText: '',
	      depositId: '',
	      drawText: '',
        drawId: '',
        currencyOptins: [
	        {text: 'CNY'},
	        {text: 'USD'},
	        {text: 'BTC'},
        ],
	      userName: '',
        id: this.$route.query.id,
        data: '',

      }
    },
    created() {
      this.getData()
    },
    methods: {
	    saveBank () {
	    	let obj = {
	    		id: this.id,
			    bossUserId: this.bossUserId,
          currency: this.currency,
			    userName: this.userName,
			    supportDeposit: this.bankId,
			    supportDraw: this.stateId
        }
	    	this.$fetch.post('/finance/bossSupport/updateBossSupport', obj).then(res => {
          if (res.code === 0) {
          	Dialog({
		          content: '保存成功',
              okFn: () => {
		          	this.$router.push('/trade/bossSupport')
              }
            })
          }
        })
      },
	    changeBank1 (v) {
	    	this.bankType = v.text
        this.bankId = v.id
      },
	    changeBank2 (v) {
		    this.stateText = v.text
        this.stateId = v.id
	    },
	    changeBank3 (v) {
		    this.currency = v.text
	    },
      getData() {
        this.$fetch.post(`/finance/bossSupport/getBossSupportDetail`,{
        	id: this.id
        }).then(res => {
          if(res.code === 0) {
            this.data = res.data.resultData
            let obj = res.data.resultData
            this.currency = obj.currency
            this.bossUserId = obj.bossUserId
            this.userName = obj.userName
            this.bankType = this.$store.state.bossList.SupportEnum[obj.supportDeposit + 1].text
            this.bankId = obj.supportDeposit
            this.stateText = this.$store.state.bossList.SupportEnum[obj.supportDraw + 1].text
            this.stateId = obj.supportDraw
          }
        })
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
